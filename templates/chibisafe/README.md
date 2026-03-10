# Caddyfile

The Chibisafe docker compose requires a Caddyfile. Use this as Caddyfile:

```
{
	servers {
		trusted_proxies static private_ranges
		client_ip_headers X-Forwarded-For X-Real-IP
	}
}
{$BASE_URL} {
	route {
		file_server * {
				root /app/uploads
				pass_thru
		}

		@api path /api/*
		reverse_proxy @api http://YOUR_SERVER_IP:8000 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		@docs path /docs*
		reverse_proxy @docs http://YOUR_SERVER_IP:8000 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}

		reverse_proxy http://YOUR_SERVER_IP:8001 {
				header_up Host {http.reverse_proxy.upstream.hostport}
				header_up X-Real-IP {http.request.header.X-Real-IP}
		}
	}
}
```

Just remember to replace YOUR_SERVER_IP with your server's actual IP address (not the docker container's).
Also remember to modify the location of where the data is stored.
