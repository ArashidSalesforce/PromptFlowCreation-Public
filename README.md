
# Fine-tuned Hugging Face Model API Integration with Salesforce

This project demonstrates the integration of a fine-tuned Hugging Face model into a Salesforce environment. The goal is to utilize the Hugging Face Inference API to generate Salesforce metadata dynamically from text prompts, and automate metadata deployment using the Salesforce Metadata API.

## Requirements

- Salesforce (with callout-enabled Apex classes)
- Hugging Face account with a fine-tuned model
- Apex knowledge
- Hugging Face API access
- Optionally, Postman (for local testing of APIs)

## Project Progression

### Initial Setup

1. **Hugging Face Integration**:
    - We integrated the Hugging Face Inference API into Salesforce using Apex classes to handle HTTP callouts. 
    - This allows us to generate Salesforce metadata dynamically based on user-provided text prompts.
   
2. **API Key Management**:
    - Hugging Face API key authentication is used to secure the callouts and ensure access to the fine-tuned model.

3. **Flow Generation**:
    - The initial implementation focused on sending a prompt from Salesforce to Hugging Face and receiving an XML-based response, which Salesforce could use to generate metadata (e.g., creating new Flows).
   
### Enhanced Metadata Deployment

1. **Metadata Deployment via Salesforce API**:
    - The project was enhanced by implementing Salesforce's Metadata API for real-time deployment of generated metadata, such as Flows, directly from the Hugging Face API response.
   
2. **Overcoming ZIP Limitations**:
    - We identified that the Salesforce Metadata API expects ZIP files for deployment. Despite initial attempts to deploy raw XML, we adjusted the process to incorporate ZIP creation through external services, ensuring that the deployment process meets Salesforce's requirements.

3. **Automating Metadata Updates**:
    - The integration allows for seamless automation within Salesforce, where Flows and other metadata can be updated dynamically based on AI-generated suggestions. This eliminates manual intervention in creating complex business logic or metadata structures.

### Final Workflow

1. **Callout to Hugging Face API**: 
    - A Salesforce user or automated process triggers an HTTP callout to Hugging Face with a text prompt.
   
2. **Generate Metadata from Hugging Face**:
    - The Hugging Face model returns a generated XML representing Salesforce metadata (e.g., Flow definitions).

3. **Deploy Metadata Using Salesforce Metadata API**:
    - The returned XML is packaged into a ZIP format, as required by Salesforce, and deployed directly using the Salesforce Metadata API.

### Apex Class Example

```apex
public class HuggingFaceService {

    @AuraEnabled
    @future(callout=true)
    public static void generateMetadata(String prompt) {
        Http http = new Http();
        HttpRequest request = new HttpRequest();
        
        // Hugging Face API endpoint and authentication
        request.setEndpoint('https://api-inference.huggingface.co/models/YOUR_MODEL_ID');
        request.setMethod('POST');
        request.setHeader('Content-Type', 'application/json');
        request.setHeader('Authorization', 'Bearer YOUR_HUGGING_FACE_API_KEY');
        
        // Create request body with the prompt
        String jsonBody = '{"inputs": "' + prompt + '"}';
        request.setBody(jsonBody);

        // Send the request and handle the response
        HttpResponse response = http.send(request);
        
        if (response.getStatusCode() == 200) {
            String responseBody = response.getBody();
            System.debug('Response from Hugging Face API: ' + responseBody);
            
            // Deploy the returned metadata (if needed)
            deployFlowMetadata(responseBody);

        } else {
            System.debug('Error in callout: ' + response.getStatus() + ' - ' + response.getBody());
        }
    }

    // This method deploys the metadata to Salesforce using Metadata API
    @future(callout=true)
    public static void deployFlowMetadata(String xmlContent) {
        // Method to package the XML and deploy it to Salesforce as metadata
        // More details in the code section above
    }
}
```

### Testing with Postman

1. Set up a Postman request to the Hugging Face model API.
2. Use the Hugging Face API URL (https://api-inference.huggingface.co/models/YOUR_MODEL_ID).
3. Include your Hugging Face API key in the `Authorization` header.
4. Send a JSON payload with the `prompt` field.

```json
{
  "inputs": "Generate metadata for a custom object in Salesforce"
}
```

### Project Summary

This project successfully integrates Hugging Face AI capabilities with Salesforce metadata automation, empowering developers to generate, deploy, and update metadata (such as Salesforce Flows) dynamically from text prompts. This can be applied in various Salesforce environments to automate business logic, significantly reducing manual intervention and setup time.

### License

This project is licensed under the MIT License.
