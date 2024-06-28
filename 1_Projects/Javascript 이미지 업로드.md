#javascript #promise #fetch #s3 #base64

1. Extract all `img` tags including Base64 image data.
2. Request your API to get a presigned URL for each Base64 data.
3. Upload each Base64 image data using the presigned URL.
4. Replace the Base64 data in `img` tags with the presigned URLs.
5. Request your other API with the modified HTML body.

### Complete Script

Here's the complete vanilla JavaScript script to achieve this:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Base64 to URL Conversion</title>
</head>
<body>

<!-- Your HTML content here -->

<script>
async function getPresignedUrl(base64Data) {
    const response = await fetch('https://your-api-endpoint.com/get-presigned-url', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ base64Data })
    });
    const data = await response.json();
    return data.presignedUrl;
}

async function uploadBase64Image(base64Data, presignedUrl) {
    const blob = base64ToBlob(base64Data);
    await fetch(presignedUrl, {
        method: 'PUT',
        body: blob
    });
}

function base64ToBlob(base64Data) {
    const byteString = atob(base64Data.split(',')[1]);
    const mimeString = base64Data.split(',')[0].split(':')[1].split(';')[0];
    const ab = new ArrayBuffer(byteString.length);
    const ia = new Uint8Array(ab);
    for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }
    return new Blob([ab], { type: mimeString });
}

async function processImages() {
    const imgTags = document.querySelectorAll('img');
    const base64ImgTags = Array.from(imgTags).filter(img => img.src.startsWith('data:image/'));

    const presignedUrlPromises = base64ImgTags.map(img => getPresignedUrl(img.src));
    const presignedUrls = await Promise.all(presignedUrlPromises);

    const uploadPromises = presignedUrls.map((url, index) => uploadBase64Image(base64ImgTags[index].src, url));
    await Promise.all(uploadPromises);

    base64ImgTags.forEach((img, index) => {
        img.src = presignedUrls[index].split('?')[0]; // Assuming presignedUrl contains the URL for direct access
    });

    sendModifiedHtmlToApi();
}

async function sendModifiedHtmlToApi() {
    const htmlContent = document.documentElement.outerHTML;
    await fetch('https://your-api-endpoint.com/send-modified-html', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ htmlContent })
    });
}

document.addEventListener('DOMContentLoaded', processImages);
</script>

</body>
</html>
```

### Explanation

1. **Extract `img` tags**:
   - Use `document.querySelectorAll('img')` to get all `img` tags.
   - Filter those with Base64 data using `.filter(img => img.src.startsWith('data:image/'))`.

2. **Request Presigned URLs**:
   - `getPresignedUrl` function sends a POST request with the Base64 data to your API to get a presigned URL.

3. **Upload Base64 Image**:
   - `uploadBase64Image` function converts the Base64 data to a `Blob` and uploads it to the presigned URL using a PUT request.

4. **Replace Base64 Data with URLs**:
   - After all presigned URLs are obtained and images are uploaded, update the `src` attribute of each `img` tag with the URL part of the presigned URL (without the query parameters).

5. **Send Modified HTML**:
   - Serialize the modified HTML using `document.documentElement.outerHTML` and send it to another API endpoint.

This script will run after the DOM content is loaded (`DOMContentLoaded` event), ensuring that all `img` tags are processed and updated correctly.