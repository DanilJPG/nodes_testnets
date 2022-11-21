Grafana is an open source platform for data visualization, monitoring and analysis.

First, you need to install the Node Exporter

Open a session with the server where some node is installed, from where you want to get data into the dashboard and install the software on that server:

#### updating the 'database'
```
sudo apt-get update && sudo apt-get upgrade -y
```
#### download 'node_exporter' and clean up with one command
```
cd $HOME && \
wget https://github.com/prometheus/node_exporter/releases/download/v1.2.0/node_exporter-1.2.0.linux-amd64.tar.gz && \
tar xvf node_exporter-1.2.0.linux-amd64.tar.gz && \
rm node_exporter-1.2.0.linux-amd64.tar.gz && \
sudo mv node_exporter-1.2.0.linux-amd64 node_exporter && \
chmod +x $HOME/node_exporter/node_exporter && \
mv $HOME/node_exporter/node_exporter /usr/bin && \
rm -Rvf $HOME/node_exporter/
```
#### create a 'exporterd' service file
```
sudo tee /etc/systemd/system/exporterd.service > /dev/null <<EOF
[Unit]
Description=node_exporter
After=network-online.target
[Service]
User=$USER
ExecStart=/usr/bin/node_exporter
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
#### run the 'exporterd' service with one command
```
sudo systemctl daemon-reload && \
sudo systemctl enable exporterd && \
sudo systemctl restart exporterd
```
#### checking the logs
```
sudo journalctl -u exporterd -f
```
You will see something similar in the terminal output:

![image](https://user-images.githubusercontent.com/57448493/198836880-35f30989-2099-4b72-814b-8b740ea50527.png)

Now in the browser on your home device go to: http://<server_IP>:9100/, where <server_IP> is the IP of the server where node_exporter is installed.

The Node Exporter will be displayed. Click on Metrics:
![image](https://user-images.githubusercontent.com/57448493/198836889-9902302f-999e-4652-b153-383a0614f4fb.png)


Here we will see ten thousand metrics that will be sent to the processing node and later displayed in the graphan. Great.

Next, we will need to set up a node to process the data.

We will need a server where the data will be sent to for processing and visualization from all our servers with nodes.

I will take a server with 1 CPU, 1 GB RAM and 20 GB of memory. If this is not enough, I will update the requirements in the future.

Open a session with our new server (node handler) and install the necessary software:

#### updating the 'database' 
```
sudo apt-get update && sudo apt-get upgrade -y
```
#### install 'prometheus' with one command
```
wget https://github.com/prometheus/prometheus/releases/download/v2.28.1/prometheus-2.28.1.linux-amd64.tar.gz && \
tar xvf prometheus-2.28.1.linux-amd64.tar.gz && \
rm prometheus-2.28.1.linux-amd64.tar.gz && \
mv prometheus-2.28.1.linux-amd64 prometheus
```
#### Open the Prometheus config to configure
```
nano $HOME/prometheus/prometheus.yml
```
In the column tagrets we need to add, after the comma, the IP address of our server with node, from which the statistics are exported.

In the future we can add an infinite number of them, but for now we will limit ourselves to one server.
![image](https://user-images.githubusercontent.com/57448493/198836904-b5917b7c-915a-4dc0-a760-de55ba02463f.png)

Save the changes and create the service.

#### granting performance rights
```
chmod +x $HOME/prometheus/prometheus
```
#### create a 'prometheusd' service file
```
sudo tee /etc/systemd/system/prometheusd.service > /dev/null <<EOF
[Unit]
Description=prometheus
After=network-online.target
[Service]
User=$USER
ExecStart=$HOME/prometheus/prometheus \
--config.file="$HOME/prometheus/prometheus.yml"
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
#### run the 'prometheusd' service with one command
```
sudo systemctl daemon-reload && \
sudo systemctl enable prometheusd && \
sudo systemctl restart prometheusd
```
#### checking the logs
```
sudo journalctl -u prometheusd -f
```
Go to: http://<server_IP>:9090/, where <server_IP> is the IP of the server where prometheus is installed.
![image](https://user-images.githubusercontent.com/57448493/198836981-60e7490b-cd90-4ecc-949c-1b3d0d415874.png)

![image](https://user-images.githubusercontent.com/57448493/198836934-cbfa8878-0f61-4822-ac55-7d0204dd0ba4.png)

Go to Status > Targets:

![image](https://user-images.githubusercontent.com/57448493/198837006-206168ac-e3a1-4c4d-bf6d-875c3cea530a.png)

Prometheus shows 2 targets: the first is the server where we installed node_exporter, the second is Prometheus itself (localhost). Perfect.

Installing Grafana

Now we install Grafana on the same server with Prometheus in order to visualize somehow all the collected statistics.

#### install the graphan with one command
```
sudo apt-get install -y adduser libfontconfig1 && \
wget https://dl.grafana.com/oss/release/grafana_8.0.6_amd64.deb && \
sudo dpkg -i grafana_8.0.6_amd64.deb
```
#### Start the 'grafana-server' service
```
sudo systemctl daemon-reload && \
sudo systemctl enable grafana-server && \
sudo systemctl restart grafana-server
```
#### looking at the logs
```
sudo journalctl -u grafana-server -f
```
![image](https://user-images.githubusercontent.com/57448493/198837057-98dc9323-b8d4-4a3e-9464-3f9711f0df0e.png)

In the browser on your home device go to: http://<server_IP>:3000/, where <server_IP> is the IP of the server where prometheus and grafana are installed.

If all goes well, we are greeted by the welcome window of grafana:

![image](https://user-images.githubusercontent.com/57448493/198837074-f10d67ae-f4e9-4e1c-a2a9-d8e994260e31.png)

Password: admin Login: admin

Now set your own password:
![image](https://user-images.githubusercontent.com/57448493/198837089-3018ccd5-f579-41bd-a371-9dfa4ec90bc9.png)


The next step is to add Data Sources. Select Prometheus:
![image](https://user-images.githubusercontent.com/57448493/198837100-3558a8ca-0a15-4708-b906-6c55461f4f74.png)

![image](https://user-images.githubusercontent.com/57448493/198837106-8b515626-826b-4cda-8e44-9009fe8d925c.png)


Specify in the URL column the address to your server with Prometheus and graphana:

![image](https://user-images.githubusercontent.com/57448493/198837117-a07aae6b-2f5f-4d13-97bf-62ab50fbca4b.png)

Then click Save & test.
![image](https://user-images.githubusercontent.com/57448493/198837130-8c953958-7df5-4b5f-9b06-663470badc14.png)

If everything is set up correctly, you will see a green checkmark:


Now we need to import the dashboard to finally see the graphs:

![image](https://user-images.githubusercontent.com/57448493/198837137-ac0766e1-78fa-4f22-80c1-906b74c2af5c.png)

To import a dashboard by ID, we first need to know that ID.

Go to the catalog of dashboards and choose any dashboard you like.

Unfortunately, not all dashboards from the catalog will work, but if you try hard, you can find working solutions. In the following article I will present my dashboard.

For example, I will specify ID - 1860.

![image](https://user-images.githubusercontent.com/57448493/198837149-85212fcf-7a05-4b57-aa21-9724a225f9ae.png)

Choosing a name for the dashboard and the Prometheus source

![image](https://user-images.githubusercontent.com/57448493/198837156-06293e29-b00e-4846-9580-43e0f6deeb13.png)
