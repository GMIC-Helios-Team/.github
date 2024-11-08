![Project Logo](./Helios-logo.png)



# Image Gen Service

### Connection info : 

**Swagger** : [swagger.yaml](https://github.com/GMIC-Helios-Team/ReadMe/blob/main/swagger.yaml)

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
