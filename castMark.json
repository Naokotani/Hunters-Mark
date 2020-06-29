//This macro must be used by the GM unless the player has permission to update
//NPC tokens, which normally is not the case.



//This marco is designed to be used in conjunction with MarkAttackWrapper.json
//It is designed to replace the standard rollItemMacro for either the Hex
//spell or the hunter's mark Spell on either the warlock or ranger, but
//it will work on an class with hex or hunter's mark by changing the Localization
//parameters. To use hex, replace warlockName variable below and if you
//would like to use it with hunter's mark replace rangerName variable below.


//Localization
const rangerName = 'Ranger';
const warlockName = 'Warlock';
const rangerSpell = "Hunter's Mark";
const warlockSpell = 'Hex';
const condition = 'Marked';
const flagScope = 'world';



//parameters
let myToken = token;
const markDmg = ' + 1d6';
const target = game.user.targets.values().next().value;
const conditionList = target.data.effects;
const actorId = myToken.actor._id+'_mark';
const bonuses = myToken.actor.data.data.bonuses;
let className = '';
let spellName = '';


//If Move flag and condition to a new selected target
function move() {
  let swapTarget = myToken.getFlag(flagScope, actorId);
  const remCon = canvas.tokens.get(swapTarget.targetId);
  swapTarget.targetId = target.data._id;
  game.cub.removeCondition(condition, remCon, {warn: true});
  (async () => {
    await myToken.unsetFlag(flagScope, actorId);
    myToken.setFlag(flagScope, actorId, swapTarget);
  })();
  game.cub.applyCondition(condition, target, {warn: true});
  console.log()
};

//revert global damage to base, remove the condition and unset flag

function remove() {
    const flagId = myToken.getFlag(flagScope, actorId);

    let obj = {
    'data.bonuses.mwak.damage': flagId.meleeAtk,
    'data.bonuses.rwak.damage': flagId.rangeAtk,
    'data.bonuses.msak.damage': flagId.meleeSpell,
    'data.bonuses.rsak.damage': flagId.rangeSpell
    }

    updateActor(myToken, obj);

    const remFlag = canvas.tokens.get(flagId.targetId);

    (async () => {
      await game.cub.removeCondition(condition, remFlag, {warn: true});
      await myToken.unsetFlag(flagScope, actorId);
    })();
};

// User input to move or remove flag and condition

function alterMark () {
  const d = new Dialog({
   title: "Mark Enemy",
   content: "<p>Would you like to move or remove?</p>",
   buttons: {
    one: {
     icon: '<i class="fas fa-check"></i>',
     label: "Move",
     callback: () => move()
    },
    two: {
     icon: '<i class="fas fa-times"></i>',
     label: "Remove",
     callback: () => remove()
     }
   },
   default: "two",
   close: () => console.log("Dialog closed")
  }).render(true);
};

// cast the spell, apply the condition, create and set flag

async function castSpell() {
  try {
  await game.dnd5e.rollItemMacro(spellName);
} catch (err) {return null}
  await game.cub.applyCondition(condition, target, {warn: true});

  let globalDmg = {
    targetId: target.data._id,
    meleeAtk: bonuses.mwak.damage,
    rangeAtk: bonuses.rwak.damage,
    meleeSpell: bonuses.msak.damage,
    rangeSpell: bonuses.rsak.damage,
    isSet: false
  };
  myToken.setFlag(flagScope, actorId, globalDmg);
};

//If the class is ranger and has the hunter's mark spell, set the spell name
//or say the spell doesn't exist.

function setRangerSpell() {
  className = rangerName;
  if (actor.items.find(i => i.name === `${rangerSpell}`)) {
      spellName = rangerSpell;
      castSpell();
  } else {
    console.log('Selected actor does not have the ' + rangerSpell +' spell.' )
    return null;
  };
};

//If the class is warlock and has the hex spell set the spell name
//if say the spell doesn't exist

function setWarlockSpell(){
  className = warlockName;
  if (actor.items.find(i => i.name === `${warlockSpell}`)){
    spellName = warlockSpell;
    castSpell();
  } else {
    console.log('Selected actor does not have the ' + warlockSpell + ' spell.')
    return null;
  };
};

//check whether the token is a ranger or warlock

function checkSpell(){
  if (actor.items.find(i => i.name === `${rangerName}`)){
    setRangerSpell();
  } else if (actor.items.find(i => i.name === `${warlockName}`)) {
    setWarlockSpell();
  } else {
    console.log('Please select a ' + rangerName + ' or ' + warlockName +' token.' )
  };
};

async function updateActor(updateToken, obj){

    await updateToken.actor.update(obj);
}

//If the flag exists call functions to move or remove if it doesn't exist
// call function to cast the spell

if (!myToken)
  ui.notifications.error("Please select your token first.");
else {
  if (myToken.getFlag(flagScope, actorId)) {
    alterMark();
  } else {
    checkSpell();
  };
}
