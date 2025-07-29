# Security Image Generator Logic App Plugin

This plugin integrates with Microsoft Security Copilot to generate security-related images and diagrams through Azure Logic App workflows.

## Updated Changes Based on Microsoft Security Copilot Plugin API Documentation

### Key Updates Made:

1. **Authentication Configuration**: 
   - Added proper `SupportedAuthTypes` and `Authorization` sections
   - Configured API Key authentication using query parameter (`code`)
   - Removed sensitive tokens from the main YAML file

2. **OpenAPI Specification**:
   - Created a separate `logic-app-openapi-spec.yaml` file with proper OpenAPI 3.0 format
   - Defined all endpoints, parameters, and responses according to OpenAPI standards
   - Added comprehensive examples and documentation

3. **Structure Compliance**:
   - Removed the custom `Skills` section (not supported in API format)
   - Followed the exact format specified in Microsoft documentation
   - All functionality is now defined in the OpenAPI spec file

### Files Structure:

```
├── security-image-generator-logicapp.yaml    # Main plugin manifest
├── logic-app-openapi-spec.yaml              # OpenAPI specification
└── README-PLUGIN-SETUP.md                   # This file
```

## Setup Instructions

### 1. Host the OpenAPI Specification

The OpenAPI specification file (`logic-app-openapi-spec.yaml`) needs to be hosted publicly. Options include:

- **GitHub**: Upload to your repository and use the raw URL
- **GitHub Gist**: Create a public gist
- **Azure Storage**: Host in a publicly accessible blob container
- **Other hosting service**: Any publicly accessible URL

### 2. Update the Plugin Manifest

In `security-image-generator-logicapp.yaml`, update the `OpenApiSpecUrl` to point to your hosted OpenAPI spec:

```yaml
Settings:
  OpenApiSpecUrl: https://raw.githubusercontent.com/iamjoeycruz/securitycopilot/main/logic-app-openapi-spec.yaml
  EndpointUrl: https://prod-20.northcentralus.logic.azure.com:443/workflows/c6927e911fcd473c8827c2d63db27be4/triggers/When_a_HTTP_request_is_received/paths/invoke
```

### 3. Configure Authentication

When uploading the plugin to Security Copilot, you'll be prompted to enter the API key (the `code` parameter from your Logic App URL):

```
Original URL: https://prod-20.northcentralus.logic.azure.com:443/workflows/.../invoke?api-version=2016-10-01&sp=...&sv=1.0&sig=2V_xa1fAklUqi1umtdzwg36Yb_AarUdT1kfMEyQuXGU

API Key to enter: 2V_xa1fAklUqi1umtdzwg36Yb_AarUdT1kfMEyQuXGU
```

### 4. Update Your Logic App

Your Logic App should be updated to handle the new request format defined in the OpenAPI spec:

**Expected Request Format:**
```json
{
  "skillName": "GenerateSecurityDiagram",
  "parameters": {
    "description": "Network topology showing DMZ, internal network, and security controls",
    "diagramType": "network",
    "style": "professional",
    "outputFormat": "png"
  }
}
```

**Expected Response Format:**
```json
{
  "success": true,
  "imageUrl": "https://example.com/generated-image.png",
  "description": "Generated network security diagram",
  "metadata": {
    "contentType": "security-diagram",
    "format": "png",
    "dimensions": "1024x768",
    "generatedAt": "2025-07-29T12:00:00Z"
  }
}
```

## Available Skills

The plugin provides three main capabilities:

### 1. GenerateSecurityDiagram
- **Description**: Creates network diagrams, threat landscapes, or security architecture visuals
- **Parameters**: description, diagramType, style, outputFormat
- **Example Prompts**: 
  - "Generate a network security diagram showing firewall architecture"
  - "Create a threat landscape diagram for our organization"

### 2. GenerateThreatActorProfile
- **Description**: Creates visual profiles and infographics for threat actors
- **Parameters**: actorName, characteristics, includeTimeline, threatLevel
- **Example Prompts**:
  - "Create a threat actor profile for APT29"
  - "Generate a visual profile for the Lazarus group"

### 3. GenerateIncidentVisualization
- **Description**: Creates visual timelines and flow diagrams for security incidents
- **Parameters**: incidentDescription, timeframe, affectedSystems, visualType, priorityLevel
- **Example Prompts**:
  - "Generate an incident timeline visualization for the recent breach"
  - "Create a visual representation of the ransomware attack"

## Security Considerations

1. **API Key Security**: The Logic App access code should be treated as a sensitive credential
2. **HTTPS Only**: All endpoints use HTTPS for secure communication
3. **Access Control**: Consider implementing additional authentication layers in your Logic App
4. **Logging**: Enable logging in your Logic App for audit and troubleshooting purposes

## Troubleshooting

### Common Issues:

1. **Authentication Errors**: Verify the API key is correctly entered in Security Copilot
2. **OpenAPI Spec Not Found**: Ensure the OpenAPI spec URL is publicly accessible
3. **Logic App Errors**: Check the Logic App run history for detailed error information
4. **Format Errors**: Verify the request/response format matches the OpenAPI specification

### Testing:

You can test your Logic App endpoint directly using tools like Postman or curl:

```bash
curl -X POST "https://prod-20.northcentralus.logic.azure.com:443/workflows/.../invoke?code=YOUR_CODE" \
  -H "Content-Type: application/json" \
  -d '{
    "skillName": "GenerateSecurityDiagram",
    "parameters": {
      "description": "Test network diagram",
      "diagramType": "network"
    }
  }'
```

## Support

For issues related to:
- **Plugin format**: Review the Microsoft Security Copilot Plugin API documentation
- **Logic App**: Check Azure Logic App documentation and run history
- **OpenAPI**: Validate your OpenAPI spec using online validators

## Version History

- **v1.0.0**: Initial version with custom Skills format (deprecated)
- **v2.0.0**: Updated to comply with Microsoft Security Copilot Plugin API standards
