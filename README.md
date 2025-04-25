# elk-stack-setup.
How to Install and Configure Elasticsearch, Kibana &amp; Logstash (8.17) on Kali Linux



 📚 What is the Elastic Stack?

The **Elastic Stack** (also known as the **ELK Stack**) is a powerful set of open-source tools used for searching, analyzing, and visualizing data in real time. It consists of:

- **Elasticsearch** – A search and analytics engine that stores and indexes data for fast searches.

- **Logstash** – A data processing pipeline that collects, transforms, and forwards data.

- **Kibana** – A visualization tool used to explore data stored in Elasticsearch.

- **Beats** (optional) – Lightweight agents that ship data to Logstash or Elasticsearch.

Together, these tools help organizations gain insights from large volumes of data — such as system logs, metrics, or application performance data — all in real time.

**Elasticsearch** is used for real-time, full-text searches in applications that require analyzing large amounts of data. When combined with tools like **Kibana**, **Logstash**, and other components, Elasticsearch can be used to monitor and manage Big Data at scale.

In this demo, we'll walk through the steps to **install and configure Elasticsearch, Kibana, and Logstash (version 8.17)** on **Ubuntu Linux** (originally tested on Kali Linux).

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/30be913c-b06e-4b24-b832-c84b94018710/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


: Add Elastic Repository PGP Key

Before installing Elasticsearch and related tools, we need to add Elastic’s **PGP (public signing) key** to ensure packages are authentic and secure.

Now, use `wget` to download and install the **Elastic public signing key**:

wget -qO - <https://artifacts.elastic.co/GPG-KEY-elasticsearch> | sudo gpg --daemon -o /usr/share/keyrings/elastic-keyring.gpg

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/5b9cbed0-6219-4c13-82cd-3290fa12dac6/user_cropped_screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 Add the Elastic APT Repository

Now that the public key is in place, the next step is to add the Elastic APT repository to your system.\
`sudo apt-get install apt-transport-https`

[[Next, Save the repository definition to ]]`/etc/apt/sources.list.d/elastic-8.x.list`[[: to add the repository to your system.]]

`echo`[[ ]]`"deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main"`[[ ]]`| sudo`[[ ]]`tee`[[ ]]`/etc/apt/sources.list.d/elastic-8.x.list`

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/4aac3e45-000a-45ac-9d1a-0705ac83c652/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 Install and Configure Elasticsearch**

First, update the package index one more time before proceeding:

sudo apt-get update

Then, install the Elasticsearch package:

sudo apt-get install elasticsearch

Allow the system to download the archive and finish the installation.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/68c8899a-5739-4961-9603-88c479e5b08d/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


: Starting Elasticsearch Service

Elasticsearch does not run until you start it. Also, when you reboot the machine, you need to rerun the Elasticsearch service as it does not start automatically.

To configure Elasticsearch to start automatically when the system boots up, run the following commands:

First, reload the systemd configuration:

`sudo systemctl daemon-reload`

`sudo systemctl enable elasticsearch.service`

After the service is enabled, Elasticsearch can be started and stopped as follows:

`sudo systemctl start elasticsearch.service`

`sudo systemctl stop elasticsearch.service`

Now, Elasticsearch will start every time you turn on or reboot the system.

In any case, if there are changes to configuration files or you need to restart Elasticsearch for any reason, use:

`sudo systemctl restart elasticsearch.service`

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/4ff6bc5b-7881-4902-bbc0-eb7a27e7a056/user_cropped_screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 How to Check Elasticsearch Status

You can also check the status of the service.

`sudo` `systemctl status elasticsearch.service`

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/90ee87e4-8b77-4371-b4e7-bbf761ccbd22/user_cropped_screenshot.webp?tl_px=0,0&br_px=1918,356&force_format=jpeg&q=100&width=1120.0)


 Step 5: Test Elasticsearch

Now that the Elasticsearch service is active, we can use `curl` to test if Elasticsearch is working.

The default listening port for Elasticsearch is **9200**, so we’ll send an HTTPS request to `localhost` and check for a response.

If `curl` is not already installed on your system, you can install it with:

`sudo apt install curl`

To verify the installation, run:

`curl --version`

Once installed, test Elasticsearch with:

`curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200` 

Remember to replace this password with your password.

You should receive a JSON response confirming that Elasticsearch is up and running.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/37079549-575d-4496-9c1c-8cb153e56733/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 Next : Install Kibana

Kibana is a graphical user interface tool used for parsing and visualizing collected log files.

To install Kibana, run the following commands:

`sudo apt-get update `

`sudo apt-get install kibana`

This will download and install Kibana on your system.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/f823517c-71f5-483e-8f32-221089540a98/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


Elasticsearch Security Configuration

When you start Elasticsearch for the first time, the following security configuration occurs automatically:

- Passwords, certificates, and keys are generated and displayed in your terminal.

To connect Kibana securely to Elasticsearch, you’ll need an **enrollment token**. You can generate this token using the built-in `elasticsearch-create-enrollment-token` tool.

First, navigate to the Elasticsearch `bin` directory:

`/usr/share/elasticsearch/`

then

`./bin/elasticsearch-create-enrollment-token`[[ ]]`-s kibana`

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/ee5dc4d5-3c21-4566-83d6-963dcbfceca7/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 Run Kibana with systemd

To configure Kibana to start automatically when the system starts, run the following commands:

First, reload the systemd configuration, then enable the Kibana service.

This setup ensures that Kibana will automatically start on boot and can be managed using `systemctl`.

`sudo /bin/systemctl daemon-reload`

`sudo /bin/systemctl enable kibana.service`

Kibana can be started and stopped as follows:

- Start the service when needed.

  [[sudo systemctl start kibana.service]]

- Stop the service if required.

  [[sudo systemctl stop kibana.service]]

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/5f80db62-010c-4d28-ab5f-60dc8e7e788b/screenshot.webp?tl_px=0,0&br_px=1120,515&force_format=jpeg&q=100)


 **Test Kibana**

To access Kibana, open a web browser and browse to the following address:

<http://localhost:5601>

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/6fe0554a-b425-4123-a3f8-71a327091572/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


. Next :Copy the token we created and paste here to configure it with Elastic

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/b68fa312-078a-43d0-a1f9-8937488daddc/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


. Successfully we can see our Elastic loading .

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/2e9328f1-9974-4723-8389-a17faf271927/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


. Elastic dashboard.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/acb57aae-2038-4e45-8272-14b2d7ba918e/screenshot.webp?tl_px=0,0&br_px=1918,882&force_format=jpeg&q=100&width=1120.0)


 Next:Install Logstash

**Logstash** is a tool that collects data from different sources. The data it collects is parsed by **Kibana** and stored in **Elasticsearch**.

Install Logstash by running the following command:

sudo apt-get install logstash

### Start and Enable Logstash

1. **Start the Logstash service:**

`sudo systemctl start logstash`

2. **Enable the Logstash service:**

`sudo systemctl enable logstash`

3. **To check the status of the service, run:**

`sudo systemctl status logstash`

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/da6f9ec7-63f0-4d71-b9d5-ea73af82bff7/screenshot.webp?tl_px=0,0&br_px=1914,878&force_format=jpeg&q=100&width=1120.0)


 This is just an example :Create and Run a Sample Logstash Configuration

**Create a config file at** `/etc/logstash/logstash-stech.conf` with the following content:

input {\
stdin { }\
}

filter {\
}

output {\
elasticsearch {\
hosts =&gt; \["<https://localhost:9200>"\]\
index =&gt; "stech-index"\
user =&gt; "elastic"\
password =&gt; "changeme"\
cacert =&gt; /etc/elasticsearch/certs/http_ca.crt\
}\
}

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/ac8ef874-c48e-42e9-99ea-72e1e3377b2a/screenshot.webp?tl_px=0,0&br_px=1914,878&force_format=jpeg&q=100&width=1120.0)


 Run the Logstash Configuration File

Navigate to the Logstash installation directory:

cd /usr/share/logstash

./bin/logstash -f /etc/logstash/logstash-stech.conf

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2025-04-14/1ae7ec24-1c02-4a6c-bb27-336b80ebfb15/screenshot.webp?tl_px=0,0&br_px=1914,878&force_format=jpeg&q=100&width=1120.0)


. 🏁 Conclusion

Setting up the Elastic Stack (Elasticsearch, Kibana, and Logstash) on Ubuntu is no small task — especially when dealing with security configurations, service management, and integration between components. But completing this first part of the project lays a **strong foundation** for building a real-time data processing and visualization pipeline.

By successfully installing and configuring each tool:

- You’ve deployed a powerful search and analytics engine with **Elasticsearch**.

- Enabled interactive data visualization using **Kibana**.

- Set up **Logstash** to collect and forward data into your stack.

This setup is a key milestone for log management, monitoring, and threat detection in any modern system — a huge win for anyone in cybersecurity or DevOps.
