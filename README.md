# An Idiotic SSL Passthrough

Didn't feel like doing anything today. So, wrote this silly reverse-proxy that doesn't terminate SSL/TLS but passes it through to the desired backends. The best (worst) thing is it's written in `Bash`, and uses only your familiar Unix tools  such as `grep` and `xxd`. Tested in Linux.

### Usage

1. Install the `isp` script in `PATH`. If you're into these things you probably know how. If you don't, feel free to [ask me](mailto:dey.somajit@gmail.com).

2. Have an executable or bash script that does the following. Whenever you give a fully qualified domain name (fqdn) to it as an argument, it spits a TCP port number or the path to some Unix domain socket. This executable may be called the `router`.

3.  Launch the reverse-proxy as follows, with `socat` 

   ```bash
   socat TCP-L:$HTTPS_PORT,fork,reuseaddr SYSTEM:"isp -d $DOMAIN /path/to/router"
   ```

   `HTTPS_PORT` is self-evident. `DOMAIN` holds the base domain, whose subdomains need to be routed. E.g. in order to route different subdomains of the form `*.example.com` to different backends, `DOMAIN=example.com`. The router's job is output the port of socket for any given subdomain, `route-me.example.com`.

### Test Drive

Let's do a little testing. The sample [`router`](/router) provided herein routes requests for `localhost` to port `9091`, for `www.localhost` to `9092`, for `localtest.me` to `9093` and the remaining to `9094`.

1. `cd` to the `isp` repo that you `git-clone`d.

2. Set up the reverse-proxy 

   ```bash
   sudo socat TCP-L:443,fork,reuseaddr \
   	SYSTEM:'./isp -d localhost -d localtest.me ./router'
   ```

3. In a new tab, set up a listener at port 9091 with  `nc -lk 9091`. Whenever it receives a request it's gonna dump it to STDOUT.

4. Using a browser or `curl` load https://localhost. Did the above listener at `9091` dump anything?

5. Similarly for other ports and domain names.

6. Edit the  [`router`](/router) to your liking and play along (as if you've got nothing else to do).





