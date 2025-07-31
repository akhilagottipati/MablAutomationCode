# MablAutomationCode
let data = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
let actions = Array.isArray(data) ? data : Object.values(data).filter(x => x?.actionId && x?.attributes);

// Rules using ActionDisplayId
const rules = [
  { ActionDisplayId: 'all', attr: 'minNumberOfAccountsAllowedDocuSign', val: 1 },
  { ActionDisplayId: 1060, attr: 'maxNumberOfAccountsAllowedDocuSign', val: 6 },
  { ActionDisplayId: 'not1060', attr: 'maxNumberOfAccountsAllowedDocuSign', val: 1 },
  { ActionDisplayId: 'all', attr: 'minNumberOfAccountsAllowedDigital', val: 1 },
  { ActionDisplayId: 1060, attr: 'maxNumberOfAccountsAllowedDigital', val: 12 },
  { ActionDisplayId: 'not1060', attr: 'maxNumberOfAccountsAllowedDigital', val: 1 },
];

let pass = [], fail = [];

for (let r of rules) {
  let targets = actions.filter(a =>
    r.ActionDisplayId === 'all' ? true :
    r.ActionDisplayId === 'not1060' ? a.actionId !== 1060 :
    a.actionId === r.ActionDisplayId
  );

  let allOk = true;

  for (let a of targets) {
    let actual = a.attributes?.[r.attr];
    if (actual !== r.val) {
      fail.push(`ActionDisplayId ${a.actionId} does not have expected value for AttributeName ${r.attr}`);
      allOk = false;
    }
  }

  if (allOk) {
    let label =
      r.ActionDisplayId === 'all' ? 'In all ActionDisplayId' :
      r.ActionDisplayId === 'not1060' ? 'For all except 1060 ActionDisplayId' :
      `For ActionDisplayId ${r.ActionDisplayId}`;
    pass.push(`${label} the AttributeName ${r.attr} has value ${r.val}`);
  }
}

pass.forEach(p => console.log(p));
fail.forEach(f => console.log(f));
if (fail.length > 0) throw new Error('Validation failed:\n' + fail.join('\n'));
