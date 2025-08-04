# MablAutomationCode




var resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
var actions = Array.isArray(resp) ? resp : Object.values(resp).filter(a => a?.actionId);

var rules = [
  { id: 1002, payload: true },
  { id: 1070, payload: true, parent: true },
  { id: 1069, payload: true, name: 'Open MAS Account - MAS Select' },
  { id: 1082, payload: true, parent: true, name: 'Open MAS Account - ThomasPartners (TPI)' },
  { id: 1083, payload: true, parent: true, name: 'Open MAS Account - Wasmer Schroeder (WS)' },
  { id: 1084, payload: true, parent: true, name: 'Open MAS Account - Windhaven (WH)' }
];

var good = [], bad = [];

rules.forEach(rule => {
  let act = actions.find(a => a.actionId === rule.id);
  if (!act) return bad.push(`Action ${rule.id} not found in response.`);

  if (rule.payload) {
    let payload = act?.actionPayload;
    if (!payload || Object.keys(payload).length === 0)
      bad.push(`Action ${rule.id} has empty or missing actionPayload.`);
    else good.push(`Action ${rule.id} has valid actionPayload.`);
  }

  if (rule.parent !== undefined) {
    if (act.isParentAction !== rule.parent)
      bad.push(`Action ${rule.id} isParentAction should be ${rule.parent}.`);
    else good.push(`Action ${rule.id} isParentAction is correct.`);
  }

  if (rule.name) {
    if (act.actionName !== rule.name)
      bad.push(`Action ${rule.id} name mismatch. Expected '${rule.name}', got '${act.actionName}'`);
    else good.push(`Action ${rule.id} name matched.`);
  }
});

good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));
if (bad.length) throw new Error('Validation failed:\n' + bad.join('\n'));
