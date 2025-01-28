# Guidelines of Running Local 7B DeepSeek R1 by Sicong Jiang 

This guide will help you quickly set up and use DeepSeek R1 via a Docker image on our lab server. Follow these steps, and you'll be chatting with one of the smartest AI models in no time!

---

## 1. Prerequisites

Before starting:

- Ensure you have SSH access to the lab server.
- Familiarize yourself with basic Docker commands.
- The Docker image **ollama_deepseekr1** is already available on the server — no need to pull it.

---

## 2. Log in to the Lab Server

Open your terminal and SSH into the lab server:

```bash


ssh your_username@server_ip
```

Replace:
- **your_username**: Your server username.
- **server_ip**: The server’s IP address.

---

## 3. Create and Start a Docker Container

The DeepSeek R1 image is named **ollama_deepseekr1**. You’ll need to create a new container using this image.

Run the container with GPU support:

```bash


docker run -it --name deepseek_container --runtime=nvidia -p 11434:11434  ollama_deepseekr1
```

Here’s what this command does:

- **--name deepseek_container**: Names the container for easy reference.  
- **--runtime=nvidia**: Ensures NVIDIA GPU support for fast processing.  
- **-p 11434:11434**: Exposes the container’s port 11434 to the server’s port 11434 for API access.  
- **ollama_deepseekr1**: The Docker image preloaded with DeepSeek R1.

After running this, you’ll enter the container and see a prompt like:

```bash


root@<container-id>:/#
```

---

## 4. Start the DeepSeek Service

In the Docker container, start the **ollama** service:

```bash


OLLAMA_HOST=0.0.0.0:11434 ollama serve
```

This command runs the API service and binds it to port 11434. You’ll see logs confirming the service is running:

```plaintext


Listening on [::]:11434
```

Keep this terminal open! It’s running the service in the background.

---

## 5. Open Another Terminal to Run the Model

You need a second terminal to interact with the model. Open another terminal and reconnect to the server:

```bash


ssh your_username@server_ip
```

Then attach to the running container:

```bash


docker exec -it deepseek_container /bin/bash
```

Inside the container, run the model:

```bash


ollama run deepseek-r1:7b
```

You can now chat directly with the model in this terminal! For example:

```plaintext


> Hello, DeepSeek!
DeepSeek: Hi there! How can I assist you today?
```

---

## 6. Optional: Interact via API

If you prefer to send requests via API instead of direct interaction, you can use tools like curl or Postman.

**Example Query**

Run this command from your local machine (replace `<server_ip>` with the server's IP address):

```bash


curl -X POST http://<server_ip>:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
        "model": "deepseek-r1:7b",
        "messages": [{"role": "user", "content": "Hello, how are you?"}]
      }'
```

**Expected Response**

The API will return a JSON response with the assistant's reply:

```json


{
  "id": "chatcmpl-001",
  "object": "chat.completion",
  "model": "deepseek-r1:7b",
  "choices": [
    {
      "message": {
        "role": "assistant",
        "content": "Hello! I'm here to help. How can I assist you?"
      }
    }
  ]
}
```

---

## 7. Stop the Container When Done

When you’re finished, stop the container to free up resources:

```bash


docker stop deepseek_container
```

If you want to restart it later, use:

```bash


docker start -ai deepseek_container
```

---

## 8. Troubleshooting

**Q: I can’t see GPU support inside the container.**

Ensure the server has the NVIDIA Container Toolkit installed:

```bash


dpkg -l | grep nvidia-container-toolkit
```

If not installed, follow these steps:

```bash


sudo apt update
sudo apt install -y nvidia-container-toolkit
sudo systemctl restart docker
```

**Q: I get a “connection refused” error.**

Make sure the ollama service is running in the container:

```bash


docker exec -it deepseek_container ps aux | grep ollama
```

Ensure the server allows access to port 11434:

```bash


sudo ufw allow 11434/tcp
sudo ufw reload
```

**Q: The model seems slow.**

Confirm the container is using the GPU:

```bash


nvidia-smi
```

If it’s running on CPU, restart the container with `--runtime=nvidia`.

---

## 9. That’s It! 🎉

Congratulations! You’re now ready to use DeepSeek R1 for all your AI needs. Whether you’re testing new ideas, brainstorming, or diving deep into AI research, DeepSeek R1 is here to help.

🚀 Enjoy experimenting with DeepSeek R1! 😊

If you need help, feel free to ask me or the lab admin.

