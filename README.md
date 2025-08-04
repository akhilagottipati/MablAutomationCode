# MablAutomationCode




var resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
var actions = Array.isArray(resp) ? resp : Object.values(resp).filter(a => a?.actionId && a?.actionName);

var rules = [
  { id: 1002, payload: true },
  { id: 1070, payload: true, name: 'Open MAS Account - MAS Select', parent: true },
  { id: 1069, absent: true },
  { id: 1082, absent: true, parent: true },
  { id: 1083, absent: true, parent: true },
  { id: 1084, absent: true, parent: true }
];

var good = [], bad = [];

rules.forEach(r => {
  let act = actions.find(a => a.actionId === r.id);

  if (r.absent) {
    act ? bad.push(`Action ${r.id} should NOT be present.`) : good.push(`Action ${r.id} correctly absent.`);
    return;
  }
  if (!act) return bad.push(`Action ${r.id} not found.`);

  if (r.payload && (!act.actionPayload || Object.keys(act.actionPayload).length === 0))
    bad.push(`Action ${r.id} has empty actionPayload.`);
  else if (r.payload) good.push(`Action ${r.id} has valid payload.`);

  if (r.parent !== undefined)
    act.isParentAction !== r.parent
      ? bad.push(`Action ${r.id} isParent should be ${r.parent}.`)
      : good.push(`Action ${r.id} isParent is correct.`);

  if (r.name && act.actionName !== r.name)
    bad.push(`Action ${r.id} name mismatch. Expected '${r.name}', got '${act.actionName}'.`);
  else if (r.name) good.push(`Action ${r.id} name is correct.`);
});

good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));
if (bad.length) throw new Error(bad.join('\n'));
