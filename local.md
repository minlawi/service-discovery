# https://developer.hashicorp.com/consul/docs/manage/dns/forwarding/enable#systemd-resolved

# Step 1
/etc/systemd/resolved.conf.d/consul.conf
[Resolve]
DNS=127.0.0.1:8600
DNSSEC=false
Domains=~consul

# Step 2
systemctl restart systemd-resolved

# Step 3
systemctl is-active systemd-resolved
active

# Step 4
resolvectl domain
Global: ~consul
Link 2 (eth0):

# Step 5
resolvectl query consul.service.consul
consul.service.consul: 127.0.0.1

-- Information acquired via protocol DNS in 6.6ms.
-- Data is authenticated: no

# Step 6
PORT=9001 "./counting-service_linux_amd64"
PORT=9000 COUNTING_SERVICE_URL="http://counting.service.consul:9001" "./dashboard-service_linux_amd64"
consul agent -dev -config-dir="./demo-config-localhost" -node=laptop

# dashboard.json

{
	"service": {
		"name": "dashboard",
		"id": "dashboard-1",
		"address": "localhost",
		"port": 9000,
		"checks": [
			{
				"http": "http://localhost:9000/",
				"interval": "10s",
				"timeout": "2s"
			}
		]
	}
}

# counting-1.json
{
	"service": {
		"name": "counting",
		"id": "counting-1",
		"address": "localhost",
		"port": 9001,
		"tags": ["primary"],
		"checks": [
			{
				"http": "http://localhost:9001/",
				"interval": "10s",
				"timeout": "2s"
			}
		]
	}
}

# counting-2.json
{}