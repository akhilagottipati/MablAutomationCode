// Wrap everything in an async function
async function runPatchWithWait() {
    // Grab the envelopeId variable from POST step
    const envelopeId = mabl.inputs.variables.StandaloneDocusignEnvId;
    console.log("Waiting before using envelopeId: " + envelopeId);

    // Function to wait
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    // Wait 15 seconds
    await sleep(15000);

    // Now you can call your PATCH request using envelopeId
    // Example (pseudo-code, replace with your actual PATCH call)
    await fetch(`https://your-api-endpoint/envelopes/${envelopeId}`, {
        method: 'PATCH',
        headers: {
            'Content-Type': 'application/json',
            // other headers
        },
        body: JSON.stringify({
            // your PATCH payload
        })
    });

    console.log("PATCH call done for envelopeId: " + envelopeId);
}

// Run the async function
runPatchWithWait();
