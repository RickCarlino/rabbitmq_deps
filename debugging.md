# Example Config

You will need to copy/paste this later:

```erlang
[
  {
    rabbit, [
      {auth_backends, [
        rabbit_auth_backend_internal,
        rabbit_auth_backend_jwt
      ]}
    ]
  },
  { rabbitmq_mqtt, [{vhost, <<"vbzcxsqr">>}] },
  { rabbitmq_web_mqtt, [{ port, 3002 }, {vhost, <<"vbzcxsqr">>}]},
  { rabbit_auth_backend_jwt, [
      { farmbot_api_key_url, "https://my.farmbot.io/api/public_key" },
      { farmbot_vhost, <<"vbzcxsqr">>}
    ]}
].
```

```bash

# START: A fresh Ubuntu 17 box.

# Update, get deps.
apt-get update
apt-get upgrade
apt-get install libsctp1 socat

# Get Erlang 19.3
wget https://packages.erlang-solutions.com/erlang/esl-erlang/FLAVOUR_1_general/esl-erlang_19.3-1~ubuntu~zesty_amd64.deb
dpkg -i esl-erlang_19.3-1~ubuntu~zesty_amd64.deb

# Should say "19.3"
cat /usr/lib/erlang/releases/19/OTP_VERSION

# Install Rabbit 3.6.12
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/rabbitmq_v3_6_12/rabbitmq-server_3.6.12-1_all.deb
dpkg -i rabbitmq-server_3.6.12-1_all.deb

# Seems to happen randomly after some installs
apt --fix-broken install

# Did it install?
cat /var/log/rabbitmq/rabbit@vultr.log # Should show logs.

# Load plugins
cd /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.12/plugins/
wget https://github.com/RickCarlino/rabbitmq_deps/raw/master/my.farmbot.io/base64url-0.0.1.ez
wget https://github.com/RickCarlino/rabbitmq_deps/raw/master/my.farmbot.io/jsx-2.8.2.ez
wget https://github.com/RickCarlino/rabbitmq_deps/raw/master/my.farmbot.io/jwt-0.1.3.ez
wget https://github.com/RickCarlino/rabbitmq_deps/raw/master/my.farmbot.io/rabbit_auth_backend_jwt-0.0.1.ez

# Create a VHost for MQTT users:
rabbitmqctl add_vhost vbzcxsqr

# Load configs
cd /etc/rabbitmq/
wget https://raw.githubusercontent.com/RickCarlino/rabbitmq_deps/master/my.farmbot.io/enabled_plugins
touch rabbitmq.config
# I like good editors when I SSH:
snap install micro --classic
# Copy/past the config at the top of the file:
micro rabbitmq.config

# Clear out logs
truncate -s 0 /var/log/rabbitmq/rabbit@vultr.log

# Restart and try things out.
shutdown -r now

# After that, check logs to see what happened...
cat /var/log/rabbitmq/rabbit@vultr.log

```
