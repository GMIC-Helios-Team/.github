![Project Logo](../Helios-logo.png)



# Helios Gallery Project - [helios.gallery](https://helios.gallery)

## Project Summary
The Helios Gallery Project is an interactive, AI-driven web application designed to generate and share custom images based on user input. Built with a dynamic user experience, the application offers image generation with custom themes, a playful Rock-Paper-Scissors game, and a gallery to view, like, and rank generated images. It leverages multiple integrations and technologies, including N8N workflows for automation, MongoDB for data storage, SendGrid for emailing, and RabbitMQ for queuing tasks.

[![DigitalOcean](https://img.shields.io/badge/DigitalOcean-Cloud-0080FF?style=for-the-badge&logo=digitalocean)](https://www.digitalocean.com/)
[![Docker](https://img.shields.io/badge/Docker-ready-2496ED?style=for-the-badge&logo=docker)](https://www.docker.com/)
[![Caddy](https://img.shields.io/badge/Caddy-http_server-1F88C0?style=for-the-badge&logo=caddy)](https://caddyserver.com/) 
[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-message_broker-FF6600?style=for-the-badge&logo=rabbitmq)](https://www.rabbitmq.com/)
[![N8N](https://img.shields.io/badge/N8N-Workflow-FF6B6B?style=for-the-badge&logo=n8n)](https://n8n.io/)
[![MongoDB](https://img.shields.io/badge/mongodb-database-47A248?style=for-the-badge&logo=mongodb)](https://www.mongodb.com/)
[![OpenAI](https://img.shields.io/badge/Openai-ai-412991?style=for-the-badge&logo=openai)](https://www.openai.com/)
[![SendGrid](https://img.shields.io/badge/SendGrid-communication-51A9E3?style=for-the-badge&logo=sendgrid)](https://www.sendgrid.com/)
[![PostHog](https://img.shields.io/badge/PostHog-observability-000000?style=for-the-badge&logo=posthog)](https://posthog.com/)
[![Grafana Alloy](https://img.shields.io/badge/grafana-observability-F46800?style=for-the-badge&logo=grafana)](https://grafana.com/docs/alloy/latest/)

## Endpoints 
### /webhook/MakeDesign
```mermaid
sequenceDiagram
    participant User
    participant Webhook as Webhook (/MakeDesign)
    participant Code as Code (Validate Input)
    participant If as If (Input Valid?)
    participant Filenames as Set Filenames
    participant OpenAI as OpenAI (Generate Design)
    participant EditImage as Edit Image
    participant UploadImage as Upload Image to S3
    participant UploadThumbnail as Upload Thumbnail to S3
    participant RespondWebhook as Respond to Webhook (200)
    participant BadRequest as Respond to Webhook (400)
    participant Discord as Discord (Send Image)

    User->>Webhook: POST /MakeDesign with name and prompt
    Webhook->>Code: Pass request body
    Code->>If: Return validation result
    If->>Filenames: Input valid
    Filenames->>OpenAI: Set filenames and send prompt
    OpenAI->>EditImage: Generate design
    EditImage->>UploadThumbnail: Resize and upload thumbnail
    UploadThumbnail->>RespondWebhook: Send 200 OK with image filenames
    OpenAI->>UploadImage: Upload image
    UploadImage->>RespondWebhook: Send 200 OK with image filenames
    RespondWebhook->>Discord: Send image URL to Discord
    
    If->>BadRequest: Input invalid
    BadRequest->>User: Return 400 Bad Request
```

### /webhook/TestPrompts
Use this endpoint to help refine images. Generated Images to upload to [Discord Channel : image-gen-dev]
```mermaid
sequenceDiagram
    participant User
    participant Webhook as Webhook (/TestPrompts)
    participant Code as Code (Validate Input)
    participant If as If (Input Valid?)
    participant SetFilenames as Set Filenames
    participant OpenAI as OpenAI (Generate Test Image)
    participant UploadImage as Upload Image
    participant RespondWebhook as Respond to Webhook (200)
    participant BadRequest as Respond to Webhook (400)
    participant Discord as Discord (Send Image)

    User->>Webhook: POST /TestPrompts with name and prompt
    Webhook->>Code: Pass request body
    Code->>If: Return validation result
    If->>SetFilenames: Input valid
    SetFilenames->>OpenAI: Set filenames and send prompt
    OpenAI->>UploadImage: Generate and upload image
    UploadImage->>RespondWebhook: Send 200 OK with image filename
    UploadImage->>Discord: Send image URL to Discord

    If->>BadRequest: Input invalid
    BadRequest->>User: Return 400 Bad Request
```
