var resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
var actions = Array.isArray(resp) ? resp : Object.values(resp).filter(a => a?.actionId);

var good = [], bad = [];

// Rules for present actions
var checks = [
  { id: 1002, payload: true },
  { id: 1070, payload: true },
  { id: 1069, payload: true, name: 'Open MAS Account - MAS Select' }
];

// Validate present rules
checks.forEach(rule => {
  let act = actions.find(a => a.actionId === rule.id);
  if (!act) return bad.push(`Action ${rule.id} not found in response.`);

  if (rule.payload) {
    let payload = act?.actionPayload;
    if (!payload || Object.keys(payload).length === 0)
      bad.push(`Action ${rule.id} has empty or missing actionPayload.`);
    else good.push(`Action ${rule.id} has valid actionPayload.`);
  }

  if (rule.name) {
    if (act.actionName !== rule.name)
      bad.push(`Action ${rule.id} name mismatch. Expected '${rule.name}', got '${act.actionName}'`);
    else good.push(`Action ${rule.id} name matched.`);
  }
});

// Check that inactive actions are not present
[1082, 1083, 1084].forEach(id => {
  if (actions.find(a => a.actionId === id))
    bad.push(`Action ${id} should NOT be present in response (inactive).`);
  else
    good.push(`Action ${id} correctly not present.`);
});

good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));
if (bad.length) throw new Error('Validation failed:\n' + bad.join('\n'));
