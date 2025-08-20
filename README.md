{
  "stepType": "api_request",
  "name": "PATCH Envelope",
  "request": {
    "method": "PATCH",
    "url": "https://your-api-base-url/envelopes/{{envelopeId}}",
    "headers": {
      "Content-Type": "application/json"
    },
    "body": {
      "status": "UPDATED"
    }
  },
  "preRequestScript": "// Fetch the envelopeId saved from POST\nconst envelopeId = mabl.inputs.variables.envelopeId;\nconsole.log('Using envelopeId: ' + envelopeId);\n\n// Wait 15 seconds before PATCH\nfunction sleep(ms) {\n  return new Promise(resolve => setTimeout(resolve, ms));\n}\nawait sleep(15000);"
}
