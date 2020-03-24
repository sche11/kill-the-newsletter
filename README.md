<h1 align="center"><a href="https://www.kill-the-newsletter.com">Kill the Newsletter!</a></h1>
<h3 align="center">Convert email newsletters into Atom feeds</h3>
<p align="center"><img alt="Convert email newsletters into Atom feeds" src="static/logo.png" width="150" /></p>
<p align="center">
<a href="https://github.com/leafac/www.kill-the-newsletter.com"><img alt="Source" src="https://img.shields.io/badge/Source---" /></a>
<a href="https://github.com/leafac/www.kill-the-newsletter.com/actions"><img alt="Continuous Integration" src="https://github.com/leafac/www.kill-the-newsletter.com/workflows/.github/workflows/main.yml/badge.svg" /></a>
</p>

# Running Locally

Install [Node.js](https://nodejs.org/) and run:

```console
$ npm install
$ npm start
```

The web server will be running at `http://localhost:8000` and the email server at `smtp://localhost:2525`.

# Deployment

1. Create a deployment SSH key pair:

   ```console
   $ ssh-keygen
   ```

   **Private key (`id_rsa`):** Add to GitHub as a **Secret** called `SSH_PRIVATE_KEY`.

   **Public key (`id_rsa.pub`):** Add to DigitalOcean and to GitHub as a **Deploy key** for the repository.

2. Create a DigitalOcean droplet:

   |                    |                           |
   | ------------------ | ------------------------- |
   | Image              | Ubuntu 18.04.3 (LTS) x64  |
   | Plan               | Starter Standard \$5/mo   |
   | Additional options | Monitoring                |
   | Authentication     | Deployment SSH Key        |
   | Hostname           | `kill-the-newsletter.com` |
   | Backups            | Enable                    |

   **Firewall**

   |               |                           |           |
   | ------------- | ------------------------- | --------- |
   | Name          | `kill-the-newsletter.com` |           |
   | Inbound Rules | ICMP                      |           |
   |               | SSH                       | 22        |
   |               | Custom                    | 25 (SMTP) |
   |               | HTTP                      | 80        |
   |               | HTTPS                     | 443       |

   **Floating IP**

3. Configure DNS in Namecheap:

   | Type    | Host  | Value                     |
   | ------- | ----- | ------------------------- |
   | `A`     | `@`   | `<droplet-ip>`            |
   | `CNAME` | `www` | `kill-the-newsletter.com` |
   | `MX`    | `@`   | `kill-the-newsletter.com` |

4. Setup the server:

   ```console
   $ ssh-add
   $ npx pm2 deploy package.json production setup
   ```

5. Migrate the existing feeds:

   ```console
   $ ssh-add
   $ ssh -A root@kill-the-newsletter.com
   root@kill-the-newsletter.com $ rsync -av <path-to-previous-feeds> /root/www.kill-the-newsletter.com/current/static/feeds/
   ```

6. Push to GitHub, which will trigger the Action that deploys the code and starts the server.
