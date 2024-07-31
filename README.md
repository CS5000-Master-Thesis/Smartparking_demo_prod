# Guide
This guide is using:
- [Cloudflare.com](https://www.cloudflare.com/) for SSL certificates
- [Digitalocean.com](https://www.digitalocean.com/) for Web hosting
- [Name.com](https://www.name.com/) for domain

You need an account on all of these. As a student you can get a free domain and web hosting for a year through [Github Student Developer Pack](https://education.github.com/pack/offers).

### Step 1: Create a new domain on Name.com
Once you have your domain, log in at Cloudflare and create a new website with your domain. 
You are then given nameservers you have to add to your domain at Name.com.
You then have to verify the nameservers are setup correctly. 

### Step 2: Create Web Hosting on Digitalocean.com
Create a new droplet on Digitalocean.com. Choose the smallest droplet and the Ubuntu image. 
Copy the IP address of the created droplet. 

### Step 3: Add DNS records for your domain and Web hosting on Cloudflare
You need to add the following DNS records on Cloudflare for your domain and web hosting to work together:

- On Cloudflare -> YOUR_WEBSITE -> DNS -> Records -> DNS Management for YOUR_WEBSITE
    - Add a new A record with the name "@" and the IP address of your droplet
    - Add a new CNAME record with the name "www" and the "@"
    - Add a new CNAME record with the name "traefik-dashboard" and the "@"
    - Add a new CNAME record with the name "impierce" and the "@"

> "@" is an alias to YOUR_WEBSITE

- On Cloudflare -> YOUR_WEBSITE -> SSL/TLS -> Overview 
    - Set your SSL/TLS encryption to Full (strict)

#### Create Cloudflare API Token (profile -> API Token)
Permissions:
- Zone: Zone: Read
- Zone: DNS: Edit
Zone Resources
- Include -> Specific Zone -> YOUR_WEBSITE

Save the token in a secure place, and we will use it later. 

### Step 4: Setup droplet 
You can use [putty](https://www.putty.org/) to get access to your droplet. Open putty and put in the IP Address of your droplet. A shell will open and login with the credentials specified when creating the droplet. 

```shell
# Install docker https://docs.docker.com/engine/install/ubuntu/
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install htpasswd
sudo apt install apache2-utils
```

### Step 5: Download and Setup project
```shell
# Download project
git clone https://github.com/CS5000-Master-Thesis/SmartParking_demo.git

# Navigate to:
cd SmartParking_demo/production

# Update traefik.yml
nano data/traefik.yml
# Update email address
# Comment out the 'caServer' (when none of them is specified we will use production server)

# Set correct permissions to acme.json
chmod 600 ./data/acme.json

# Create password for traefik dashboard
echo $(htpasswd -nB user) | sed -e s/\\$/\\$\\$/g
# Example -> TRAEFIK_DASHBOARD_CREDENTIALS=admin:$$2y$$05$$qQZg.q5e8BzZk8rjIXH3GutfYY8wEu79U5JjB6ebwjM8YskxvnSmy

# Update .env file:
CF_DNS_API_TOKEN=YOUR_CLOUDFLARE_API_TOKEN
TRAEFIK_DASHBOARD_CREDENTIALS=YOUR_GENERATED_HTPASSWD
```

# Step 6: Run docker compose 
```shell
# Run
docker compose up -d
```
