# ALLCHAT

Node.js backend and a React MUI frontend for an application that interacts with the Gemini Pro 1.5 model, with history, image generating/recognition, PDF/Word/Excel upload and markdown support. Written fully by _Claude 3 Sonnet_.

![image](https://github.com/msveshnikov/allchat/assets/8682996/42b2e4f2-b91b-4712-8ef2-630ebb8919e9)

## Demo

https://allchat.online/

## Features

- Gemini Pro 1.5 and Claude 3 Haiku
- Multiple chats
- File upload PDFs, Word, Excel into the conversation.
- Computer Vision (png/jpeg)
- Markdown formatting.
- Image Generation by Amazon Titan.
- Python Code Run
- Haiku functions (tools)
- ... please create a Request in Issues for more ...


## Environment variables

You have to get those APIs and set environment variables (or put to .env file):

-   GEMINI_KEY - key for 1.5 preview access from https://aistudio.google.com/app/apikey?utm_source=newsletter&utm_medium=email&utm_campaign=1.5p-api-launch_march&utm_content=
-   AWS_SECRET_KEY
-   AWS_ACCESS_KEY - (for Titan image generation) - https://eu-central-1.console.aws.amazon.com/console/home?region=eu-central-1
-   CLAUDE_KEY - Anthropic Key (for Haiku)

# DOCKER DEPLOY

To containerize the Node.js backend and React MUI frontend for easy deployment, we can use Docker. Here's how you can create Docker containers for your application:

**Backend (Node.js)**
1. Replace `export const ALLOWED_ORIGIN = ["https://allchat.online", "http://localhost:3000"];` in server/index.js with your domain
2. Build the Docker image by running the following command in the backend directory:

```
docker build -t allchat-backend .
```

Replace `allchat-backend` with your desired image name. Push to Hub if needed.

**Frontend (React MUI)**

1. Replace `export const API_URL = process.env.NODE_ENV === "production" ? "https://allchat.online/api" : "http://localhost:5000";` in src/components/Main.js with your domain
2. Build the Docker image by running the following command in the frontend directory:

```
docker build -t allchat-frontend .
```

Replace `allchat-frontend` with your desired image name. Push to Hub if needed.

**Running the Containers**

After building the Docker images, you can run the containers using Docker Compose.
Make sure to replace `allchat-backend` and `allchat-frontend` with the names you used when building the Docker images.

Now, you can start the containers by running the following command in the root directory of your project:

```
docker-compose up
```

This will start both the backend and frontend containers, and the frontend will be accessible at `http://localhost`.

With this setup, you can easily deploy your containerized application to any Docker-compatible environment, such as a cloud platform or a local server.

# NGINX

To configure Nginx as a reverse proxy for your containerized Node.js backend and React MUI frontend applications, you can follow these steps:

1. **Install Nginx**

If Nginx is not already installed on your VM instance, you can install it using the appropriate package manager for your operating system. For example, on Ubuntu or Debian-based distributions, you can run:

```
sudo apt-get update
sudo apt-get install nginx
```

2. **Configure Nginx**

Open the default Nginx configuration file, usually located at `/etc/nginx/nginx.conf` or `/etc/nginx/conf.d/default.conf`. You can use a text editor like `nano` or `vim`.

```
sudo nano /etc/nginx/conf.d/default.conf
```

3. **Configure Server Blocks**

Next, configure the server blocks for your domain. Inside the `http` block, add the following:

```nginx
server {
    server_name allchat.online www.allchat.online;

    location /api/ {
        proxy_pass http://localhost:6000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP       $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location / {
        proxy_pass http://localhost:8585;
        proxy_set_header Host $host;
	    proxy_set_header X-Real-IP       $remote_addr;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/allchat.online/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/allchat.online/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

```

This configuration listens on port 80 for requests to `allchat.online` and `www.allchat.online`. It forwards requests to `/api` to your backend container, and all other requests to your frontend container.

5. **Restart Nginx**

Save the changes to the configuration file, and restart Nginx for the changes to take effect:

```
sudo systemctl restart nginx
```

After completing these steps, your Nginx server should now be correctly configured to act as a reverse proxy for your containerized Node.js backend and React MUI frontend applications, serving them at `allchat.online` and `www.allchat.online`.

Note: Make sure that your backend and frontend containers are running and accessible from your Nginx server. You may need to adjust the firewall rules or security groups on your VM instance to allow incoming traffic on the necessary ports.


[![Stargazers repo roster for @msveshnikov/allchat](https://reporoster.com/stars/msveshnikov/allchat)](https://github.com/msveshnikov/allchat/stargazers)

[![Forkers repo roster for @msveshnikov/allchat](https://reporoster.com/forks/msveshnikov/allchat)](https://github.com/msveshnikov/allchat/network/members)
