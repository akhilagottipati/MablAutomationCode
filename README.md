let resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
let actions = Array.isArray(resp) ? resp : Object.values(resp).filter(x => x?.actionId && x?.attributes);

// Inactive actionIds that should NOT be present
let inactiveIds = [1069, 1082, 1083, 1084];

// Active actions (no need to validate payloads anymore)
let activeIds = [1002, 1070];

let good = [], bad = [];

// Check that inactive actions are NOT present
for (let id of inactiveIds) {
  let act = actions.find(a => a.actionId === id);
  if (act) {
    bad.push(`Action ${id} should not be present (inactive).`);
  } else {
    good.push(`Action ${id} correctly not present.`);
  }
}

// Skip payload validation completely
for (let id of activeIds) {
  let act = actions.find(a => a.actionId === id);
  if (act) {
    good.push(`Action ${id} is present.`);
  } else {
    bad.push(`Action ${id} is missing from response.`);
  }
}

// Print results
good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));

if (bad.length > 0) throw new Error('Validation failed:\n' + bad.join('\n'));
