# MablAutomationCode
let resp2 = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
let actions2 = Array.isArray(resp2) ? resp2 : Object.values(resp2).filter(x => x?.actionId && x?.attributes);

// Define your rules for ActionDisplayId 1060
var rules1060 = [
  { attr: 'minNumberOfRecipientsAllowedDocuSign', val: 1 },
  { attr: 'maxNumberOfRecipientsAllowedDocuSign', val: 6 },
  { attr: 'minNumberOfRecipientsAllowedDigital', val: 1 },
  { attr: 'maxNumberOfRecipientsAllowedDigital', val: 10 },
  { attr: 'minNumberOfAccountsAllowedDocuSign', val: 1 },
  { attr: 'maxNumberOfAccountsAllowedDocuSign', val: 6 },
  { attr: 'minNumberOfAccountsAllowedDigital', val: 1 },
  { attr: 'maxNumberOfAccountsAllowedDigital', val: 12 },
  { attr: 'deliveryChannel', val: ['Digital', 'DocuSign'] },
  { attr: 'adviceNeeded', val: 'M' }
];

let good = [], bad = [];

let action1060 = actions2.find(a => a.actionId === 1060);

if (!action1060) {
  throw new Error('ActionDisplayId 1060 not found in response.');
}

for (let rule of rules1060) {
  let actual = action1060.attributes?.[rule.attr];

  // Special handling for deliveryChannel as array comparison
  if (Array.isArray(rule.val)) {
    let match = Array.isArray(actual) && rule.val.every(v => actual.includes(v));
    if (!match) bad.push(`${rule.attr} mismatch for 1060`);
    else good.push(`${rule.attr} matches for 1060`);
  }
  else {
    if (actual !== rule.val) bad.push(`${rule.attr} mismatch for 1060`);
    else good.push(`${rule.attr} matches for 1060`);
  }
}

good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));
if (bad.length > 0) throw new Error('Validation failed:\n' + bad.join('\n'));
