let resp = typeof responseBody === 'string' ? JSON.parse(responseBody) : responseBody;
let actions = Array.isArray(resp) ? resp : Object.values(resp).filter(x => x?.actionId);
let idsInResponse = actions.map(a => a.actionId);

let mustHaveWithPayload = [1002, 1070];
let mustNotExist = [1069, 1082, 1083, 1084];

let good = [], bad = [];

for (let id of mustHaveWithPayload) {
  let act = actions.find(a => a.actionId === id);
  if (!act) {
    bad.push(`Action ${id} is missing in response.`);
  } else {
    console.log(`Action ${id} actionPayload:`, JSON.stringify(act.actionPayload));
    if (!act.actionPayload || Object.keys(act.actionPayload).length === 0) {
      bad.push(`Action ${id} has empty or missing actionPayload.`);
    } else {
      good.push(`Action ${id} has valid actionPayload.`);
    }
  }
}

for (let id of mustNotExist) {
  if (idsInResponse.includes(id)) {
    bad.push(`Action ${id} should NOT be in response (inactive).`);
  } else {
    good.push(`Action ${id} correctly not present (inactive).`);
  }
}

good.forEach(g => console.log(g));
bad.forEach(b => console.error(b));

if (bad.length > 0) throw new Error("Validation failed:\n" + bad.join("\n"));
