# AWS CloudWatch – Hands-on Lab: EC2 CPU Monitoring

## 1. Lab Objective

In this practical, we will launch an Amazon Linux EC2 instance and deploy a simple web application using EC2 User Data.

The web application will provide buttons to increase and decrease CPU utilization.

Using Amazon CloudWatch, we will:

1. Launch an EC2 instance using Amazon Linux.
2. Deploy a web application using User Data.
3. Generate CPU workload using the web application.
4. Monitor CPU utilization using CloudWatch.
5. Increase and decrease CPU workload and observe the monitoring graph.
6. Understand Standard Monitoring versus Detailed Monitoring.
7. Understand why memory utilization is not available by default.

---

## 2. Launch an EC2 Instance

### Step 1: Open EC2 Dashboard

1. Login to AWS Management Console.
2. Navigate to EC2.
3. Click Launch Instance.

### Step 2: Configure EC2 Instance

Use the following configuration:

| Configuration | Value |
|---|---|
| Name | CloudWatch-Demo |
| AMI | Amazon Linux 2023 |
| Instance Type | t3.small |
| Key Pair | Create or select an existing key pair |
| Storage | 8 GB |
| Security Group | Allow HTTP (80) and SSH (22) |
| Monitoring | Standard Monitoring |

### Security Group Configuration

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| HTTP | 80 | My IP |

**Note:** Restrict access to your own IP because the application provides CPU workload control functionality.

---

## 3. Deploy Web Application Using User Data

During EC2 instance creation:

1. Expand Advanced Details.
2. Locate User Data.
3. Copy and paste the following script.

### EC2 User Data Script

The script will:

- Install Python 3.
- Create a simple web application.
- Provide three buttons to control CPU utilization.
- Start CPU-intensive processes.
- Stop CPU-intensive processes.
- Automatically stop workload after two minutes.
- Configure the application as a systemd service.

```bash
#!/bin/bash

dnf install -y python3

mkdir -p /opt/cpu-monitor

cat > /opt/cpu-monitor/app.py <<'PYTHON'
from http.server import BaseHTTPRequestHandler, ThreadingHTTPServer
from multiprocessing import Process
from threading import Lock, Timer
import multiprocessing
import json
import os

workers = []
lock = Lock()
stop_timer = None

MAX_WORKERS = min(os.cpu_count() or 1, 4)

def cpu_burn():
    x = 123456789
    while True:
        x = (x * x + 12345) % 2147483647

def stop_load():
    global workers, stop_timer

    with lock:
        old_workers = workers
        workers = []
        stop_timer = None

    for p in old_workers:
        if p.is_alive():
            p.terminate()

    for p in old_workers:
        p.join(timeout=2)

def start_load(count):
    global workers, stop_timer

    if count < 1 or count > MAX_WORKERS:
        return False, "Invalid worker count"

    with lock:
        if any(p.is_alive() for p in workers):
            return False, "CPU workload is already running"

        workers = []

        for _ in range(count):
            p = Process(target=cpu_burn)
            p.start()
            workers.append(p)

        if stop_timer:
            stop_timer.cancel()

        stop_timer = Timer(120, stop_load)
        stop_timer.start()

    return True, f"Started {count} CPU worker(s) for 120 seconds"

class Handler(BaseHTTPRequestHandler):

    def send_response_json(self, status, message):
        self.send_response(status)
        self.send_header("Content-Type", "application/json")
        self.send_header("Cache-Control", "no-store")
        self.end_headers()
        self.wfile.write(json.dumps({
            "message": message
        }).encode())

    def do_GET(self):

        if self.path == "/":
            html = """
            <!DOCTYPE html>
            <html>
            <head>
                <title>AWS CloudWatch CPU Lab</title>
                <meta name="viewport"
                      content="width=device-width, initial-scale=1">

                <style>
                    body {
                        font-family: Arial, sans-serif;
                        background: #f1f5f9;
                        text-align: center;
                        padding: 30px;
                    }

                    .container {
                        background: white;
                        max-width: 650px;
                        margin: auto;
                        padding: 30px;
                        border-radius: 12px;
                        box-shadow: 0 4px 15px #0002;
                    }

                    h1 {
                        color: #232f3e;
                    }

                    button {
                        padding: 14px 22px;
                        margin: 10px;
                        border: none;
                        border-radius: 7px;
                        color: white;
                        cursor: pointer;
                        font-size: 16px;
                    }

                    .low { background: #16a34a; }
                    .high { background: #ea580c; }
                    .stop { background: #dc2626; }

                    button:hover {
                        opacity: 0.85;
                    }

                    #status {
                        margin-top: 25px;
                        padding: 15px;
                        background: #e2e8f0;
                        border-radius: 7px;
                    }

                    .note {
                        color: #64748b;
                        font-size: 13px;
                    }
                </style>
            </head>

            <body>

                <div class="container">

                    <h1>AWS CloudWatch</h1>
                    <h2>EC2 CPU Monitoring Lab</h2>

                    <p>
                    Use the buttons below to generate CPU workload
                    on your EC2 instance.
                    </p>

                    <h3>Increase CPU Utilization</h3>

                    <button class="low"
                        onclick="control('start', 1)">
                        Start Low CPU Load
                    </button>

                    <button class="high"
                        onclick="control('start', MAX_WORKERS)">
                        Start High CPU Load
                    </button>

                    <h3>Decrease CPU Utilization</h3>

                    <button class="stop"
                        onclick="control('stop', 0)">
                        Stop CPU Load
                    </button>

                    <div id="status">
                        Status: Ready
                    </div>

                    <p class="note">
                    Workload automatically stops after 2 minutes.
                    </p>

                    <p class="note">
                    Maximum available workers: MAX_WORKERS
                    </p>

                </div>

                <script>
                    async function control(action, workers) {

                        const status = document.getElementById("status");

                        status.innerText = "Processing...";

                        try {
                            const response = await fetch("/api/" + action, {
                                method: "POST",
                                headers: {
                                    "Content-Type": "application/json"
                                },
                                body: JSON.stringify({
                                    workers: workers
                                })
                            });

                            const result = await response.json();

                            status.innerText = result.message;

                        } catch (error) {
                            status.innerText =
                                "Unable to communicate with server";
                        }
                    }
                </script>

            </body>
            </html>
            """

            html = html.replace(
                "MAX_WORKERS",
                str(MAX_WORKERS)
            )

            self.send_response(200)
            self.send_header("Content-Type", "text/html")
            self.end_headers()
            self.wfile.write(html.encode())

        else:
            self.send_error(404)

    def do_POST(self):

        if self.path not in ("/api/start", "/api/stop"):
            self.send_error(404)
            return

        if self.path == "/api/start":
            try:
                length = int(self.headers.get("Content-Length", 0))

                if length > 100:
                    self.send_error(400)
                    return

                data = json.loads(
                    self.rfile.read(length) or b"{}"
                )

                count = int(data.get("workers", 0))

                success, message = start_load(count)

                self.send_response_json(
                    200 if success else 409,
                    message
                )

            except (ValueError, TypeError, json.JSONDecodeError):
                self.send_response_json(400, "Invalid request")

        elif self.path == "/api/stop":

            stop_load()

            self.send_response_json(
                200,
                "CPU workload stopped successfully"
            )

server = ThreadingHTTPServer(("0.0.0.0", 80), Handler)
server.serve_forever()
PYTHON

cat > /etc/systemd/system/cpu-monitor.service <<'SERVICE'
[Unit]
Description=CloudWatch CPU Monitoring Demo
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/cpu-monitor
ExecStart=/usr/bin/python3 /opt/cpu-monitor/app.py
Restart=always

[Install]
WantedBy=multi-user.target
SERVICE

systemctl daemon-reload
systemctl enable --now cpu-monitor.service

echo "CloudWatch CPU Monitoring Application Installed"
```

### Step 4: Launch Instance

Click Launch Instance.

Wait until:

- Instance State = Running
- Status Checks = 2/2 checks passed

---

## 4. Access the Web Application

### Step 1: Copy Public IP Address

Navigate to:

EC2 → Instances → CloudWatch-Demo

Copy the Public IPv4 address.

### Step 2: Open Application

Open your browser and enter:

```text
http://<EC2-PUBLIC-IP>
```

You should see the following application interface:

**AWS CloudWatch – EC2 CPU Monitoring Lab**

The application provides three buttons:

| Button | Function |
|---|---|
| Start Low CPU Load | Starts one CPU-intensive process |
| Start High CPU Load | Starts multiple CPU-intensive processes |
| Stop CPU Load | Stops all CPU-intensive processes |

Each workload runs for a maximum of 120 seconds before automatically stopping.

---

## 5. Monitor CPU Utilization Using CloudWatch

Now let's monitor our EC2 instance using Amazon CloudWatch.

### Step 1: Open CloudWatch

Navigate to:

AWS Console → CloudWatch.

### Step 2: Navigate to Metrics

Select:

CloudWatch → Metrics → All metrics → EC2.

Select:

**Per-Instance Metrics**

### Step 3: Select Your EC2 Instance

1. Locate your EC2 instance using its Instance ID.
2. Find the CPUUtilization metric.
3. Select the metric.
4. Observe the CPU utilization graph.

### Step 4: Increase CPU Utilization

Go back to the web application.

Click:

**Start Low CPU Load**

Observe the CPUUtilization graph in CloudWatch.

You should notice an increase in CPU utilization.

Now click:

**Start High CPU Load**

This will start multiple CPU-intensive processes.

Observe the CPU utilization graph again.

You should notice a further increase in CPU utilization, depending on the instance's available CPU capacity.

### Step 5: Decrease CPU Utilization

Click:

**Stop CPU Load**

All CPU-intensive processes will be stopped.

Wait for the next CloudWatch metric data point.

Observe the graph again.

You should notice a decrease in CPU utilization.

### Expected Observation

| Activity | Expected CPU Behaviour |
|---|---|
| Application Idle | Low CPU utilization |
| Start Low CPU Load | CPU utilization increases |
| Start High CPU Load | CPU utilization may increase further |
| Stop CPU Load | CPU utilization decreases |

**Important:** CloudWatch metrics are not updated instantly. The monitoring interval depends on the EC2 monitoring configuration.

---

## 6. Standard Monitoring vs Detailed Monitoring

By default, EC2 instances use Standard Monitoring.

### Standard Monitoring

- Enabled by default.
- Publishes EC2 metrics at 5-minute intervals.
- No additional charge for detailed monitoring under this configuration.

For example, if we generate CPU workload for two minutes, the CloudWatch graph may not capture the complete workload as a separate data point.

### Detailed Monitoring

Detailed Monitoring provides more frequent EC2 metrics.

- Publishes EC2 metrics at 1-minute intervals.
- Additional charges apply.
- Useful when we need more granular monitoring.

### Enable Detailed Monitoring

Follow these steps:

1. Navigate to EC2.
2. Select Instances.
3. Select your EC2 instance.
4. Open the Monitoring tab.
5. Click Manage detailed monitoring.
6. Enable Detailed CloudWatch monitoring.
7. Save the changes.

Now navigate back to:

CloudWatch → Metrics → EC2 → Per-Instance Metrics → CPUUtilization.

You should now receive metrics at one-minute intervals after the setting takes effect.

### Comparison

| Feature | Standard Monitoring | Detailed Monitoring |
|---|---|---|
| Default | Yes | No |
| Metric Frequency | 5 Minutes | 1 Minute |
| Additional Detailed Monitoring Charge | No | Yes |
| Use Case | General Monitoring | Granular Monitoring |

**Remember:** Detailed Monitoring provides one-minute metrics, not sub-minute metrics.

---

## 7. Important Concept: Why Can't We See Memory Utilization?

While monitoring EC2 metrics in CloudWatch, you can see metrics such as:

- CPUUtilization
- NetworkIn
- NetworkOut
- DiskReadOps
- DiskWriteOps

However, you will not find Memory Utilization by default.

### Why?

EC2 default CloudWatch monitoring provides infrastructure-level metrics, but operating-system-level metrics such as memory utilization are not automatically published.

To monitor memory utilization, we need to:

1. Install the Amazon CloudWatch Agent inside the EC2 instance.
2. Configure the agent to collect operating-system metrics.
3. Publish those metrics to CloudWatch.

The CloudWatch Agent collects OS-level information such as memory and disk utilization.

**Note:** Installing the CloudWatch Agent is a separate activity and is not covered in this lab.

---

## 8. Troubleshooting

### Issue 1: Web Application Is Not Opening

Check the following:

- EC2 instance is running.
- HTTP port 80 is allowed in the Security Group.
- User Data script executed successfully.
- Application service is running.

Check the service:

```bash
sudo systemctl status cpu-monitor
```

Check User Data logs:

```bash
sudo cat /var/log/cloud-init-output.log
```

### Issue 2: CPU Utilization Is Not Increasing

Check the running processes:

```bash
ps -ef | grep app.py
```

Check the application service:

```bash
sudo systemctl status cpu-monitor
```

Check the CPU utilization locally:

```bash
top
```

### Issue 3: CPU Graph Is Not Updating Frequently

Check the EC2 monitoring configuration.

- Standard Monitoring: 5-minute interval.
- Detailed Monitoring: 1-minute interval.

Also check the CloudWatch graph's selected time range and period.

---

## 9. Cleanup

After completing the practical:

1. Click Stop CPU Load in the web application.
2. Navigate to EC2.
3. Select your instance.
4. Terminate the EC2 instance.
5. Remove unused security groups and key pairs if they were created only for this lab.

Remember that EC2, EBS storage and Detailed Monitoring can incur charges depending on usage and configuration.

---

## 10. Lab Summary

In this practical, we learned:

1. How to launch an Amazon Linux EC2 instance.
2. How to deploy a web application using User Data.
3. How to generate CPU workload using application buttons.
4. How to monitor CPU utilization using CloudWatch.
5. How to increase and decrease CPU utilization.
6. How Standard Monitoring works.
7. How to enable Detailed Monitoring.
8. Why memory utilization is not available by default.
9. Why the CloudWatch Agent is required for OS-level monitoring.

---

## 11. Interview Questions

**Q1. Does CloudWatch monitor EC2 CPU utilization by default?**

Yes. EC2 publishes CPUUtilization as a standard CloudWatch metric.

**Q2. What is the default EC2 monitoring interval?**

Five minutes under Standard Monitoring.

**Q3. What is Detailed Monitoring?**

Detailed Monitoring provides EC2 metrics at one-minute intervals and incurs additional charges.

**Q4. Why can't we see memory utilization in CloudWatch by default?**

Because operating-system memory metrics are not included in the default EC2 metrics.

**Q5. How can we monitor EC2 memory utilization?**

Install and configure the Amazon CloudWatch Agent.

**Q6. What is the difference between Standard and Detailed Monitoring?**

Standard Monitoring publishes EC2 metrics every five minutes, while Detailed Monitoring publishes them every minute.

---
