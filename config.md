# 1️⃣ Install Elasticsearch

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
```

sudo apt update

    sudo apt update
    sudo apt install elasticsearch

Enable & start the service:

    sudo systemctl enable --now elasticsearch

Verify it works:

    curl -k -u elastic https://localhost:9200/

# If authenticato error reset Elascti user password 

    sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

try agian 

    curl -k -u elastic https://localhost:9200/
    enter password

# Create new super user 

    Create the superuser role
```bash
    curl -k -u elastic 'https://localhost:9200/_security/user/ideahub' \
      -H 'Content-Type: application/json' \
      -d '{
        "password" : "passwoerd input",
        "roles" : [ "superuser" ],
        "full_name" : "Admin",
        "email" : "admin@test.local",
        "enabled": true
      }'
```

check again

    curl -k -u username:passwd https://localhost:9200/

    
    {"created":true}


# 2️⃣ Install Kibana

    sudo apt install kibana
    sudo systemctl enable --now kibana
    http://<your-ip>:5601/

# Edit  kibana.yml file 
    nano /etc/kibana/kibana.yml

    server.host: "0.0.0.0"
    server.port: 5601
    
Create Kibana enrollment token:

    sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana


Use the token in the Kibana browser UI to enroll & complete setup.

When prompted for the verification code, get it from the Kibana server:

    sudo /usr/share/kibana/bin/kibana-verification-code


# 3️⃣ Install Logstash

    sudo apt install logstash
    sudo systemctl enable --now logstash

# 4️⃣ Enable Firewall Ports

    sudo ufw allow 22/tcp
    sudo ufw allow 5601/tcp
    sudo ufw allow 9200/tcp
    sudo ufw allow 5044/tcp
    sudo ufw reload

# 5️⃣ Set up Elasticsearch Passwords & Enrollment Token

Generate/reset password for elastic:

    sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

Create Kibana enrollment token:

    sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana


Use the token in the Kibana browser UI to enroll & complete setup.

When prompted for the verification code, get it from the Kibana server:

    sudo /usr/share/kibana/bin/kibana-verification-code


# 6️⃣ Reduce Memory Usage (JVM Heap)

    sudo nano /etc/elasticsearch/jvm.options


Input:

    -Xms256m
    -Xmx256m

Restat:

    sudo systemctl restart elasticsearch

Logstash:

    sudo nano /etc/logstash/jvm.options

Paste/Edit:

    -Xms256m
    -Xmx256m


Restat:

    sudo systemctl restart logstash

# 7️⃣ Reduce Logstash Pipeline Workers

    sudo nano /etc/logstash/logstash.yml

Add:

    pipeline.workers: 1
    pipeline.batch.size: 50
    pipeline.batch.delay: 10

Restart:

    sudo systemctl restart logstash


# 8️⃣ Set CPU Quota at OS Level 

    sudo systemctl edit --full elasticsearch.service

Under [Service], add:

    # as per requrement and capacity
    CPUQuota=50%

For Logstash:

    sudo systemctl edit --full logstash.service

Under [Service], add:

    # as per requrement and capacity
    CPUQuota=50%

Apply changes:


    sudo systemctl daemon-reexec
    sudo systemctl restart elasticsearch
    sudo systemctl restart logstash



