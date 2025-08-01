# MablAutomationCode
// Parse response safely
var resp2 = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;

// Get actions array from response
var actions2 = Array.isArray(resp2) ? resp2 : Object.values(resp2).filter(x => x?.actionId && x?.attributes);

// Define validation rules for ActionDisplayId 1060
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

// Initialize result lists
var good = [], bad = [];

// Find action with ActionDisplayId 1060
var action1060 = actions2.find(a => a.actionId === 1060);

if (!action1060) {
  throw new Error("ActionDisplayId 1060 not found in response.");
}

// Apply rules
for (let rule of rules1060) {
  let actual = action1060.attributes?.[rule.attr];

  if (Array.isArray(rule.val)) {
    let match = Array.isArray(actual) && rule.val.every(v => actual.includes(v));

    if (!match) {
      bad.push(`Attribute: ${rule.attr}, Expected: [${rule.val}], Actual: [${actual}]`);
    } else {
      good.push(`Attribute: ${rule.attr}, Value matched: [${actual}]`);
    }

  } else {
    if (actual !== rule.val) {
      bad.push(`Attribute: ${rule.attr}, Expected: ${rule.val}, Actual: ${actual}`);
    } else {
      good.push(`Attribute: ${rule.attr}, Value matched: ${actual}`);
    }
  }
}

// Print results
good.forEach(g => console.log(g));
bad.forEach(b => console.log(b));

// Throw error if any validation failed
if (bad.length > 0) {
  throw new Error('Validation failed:\n' + bad.join('\n'));
}
