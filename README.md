let resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
let actions = Array.isArray(resp) ? resp : Object.values(resp).filter(x => x?.actionId);
let idsInResponse = actions.map(a => a.actionId);

let checkPayload = [1002, 1070];
let mustBeAbsent = [1069, 1082, 1083, 1084];

let good = [], bad = [];

// Check 1002 & 1070 (optional, but if present, payload must be valid)
for (let id of checkPayload) {
  let act = actions.find(a => a.actionId === id);
  if (!act) {
    good.push(`Action ${id} is not present (no validation needed).`);
  } else if (!act.actionPayload || Object.keys(act.actionPayload).length === 0) {
    bad.push(`Action ${id} is present but has empty/missing actionPayload.`);
  } else {
    good.push(`Action ${id} is present with valid actionPayload.`);
  }
}

// Check inactive actions must NOT be present
for (let id of mustBeAbsent) {
  if (idsInResponse.includes(id)) {
    bad.push(`Action ${id} should NOT be in response (inactive).`);
  } else {
    good.push(`Action ${id} correctly not present (inactive).`);
  }
}

// Output
good.forEach(g => console.log(g));
bad.forEach(b => console.error(b));

if (bad.length > 0) throw new Error("Validation failed:\n" + bad.join("\n"));
