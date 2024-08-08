# AI Dungeon: Heroes System

## Introduction

The Heroes System is more of a game than the Phoenix system in production. It tracks the game world and a lot more outside the standard input output model with several call backs to the AI to updates aspects of the state.

For this setting we are creating a new scenario Star Wars: The Imperial Cadet. The scenario is set in the Star Wars universe and follows the story of a young Imperial Cadet as they navigate the challenges of the Imperial Academy and the Galactic Empire. The player will have the opportunity to make choices that will shape their character's destiny and determine the outcome of the story.

## Configuration

Heroes System is configured with JSON files. Each Heroes worlds has several JSON files that define the world, characters, and other aspects of the game. The JSON files are Perks, Races, Skills, Classes, Triggers, WorldLore, BackstoryArcs, ClassSettings, ConfigVersion, OtherSettings, SkillSettings, StorySettings, AttributeSettings, GenerateStartInstructions, and GenerateStoryInstructions.

### Excerpts from the READMEs

#### Perks

The Perk object contains the following properties:

- `cost` (int): The cost of the perk in skill points.
- `name` (string): The name of the perk.
- `type` (string): The type of the perk (e.g., active, passive).
- `source` (string): The skill source of the perk.
- `cooldown` (int): The cooldown time of the perk in turns.
- `description` (string): The description of the perk.
- `requirements` (list): A list of dictionaries containing the requirements for the perk. Each dictionary contains the following keys:
  - `type` (string): The type of requirement (e.g., skill, item).
  - `amount` (int): The amount required for the requirement.
  - `variable` (string): The variable associated with the requirement.
- `sourceSpecific` (string): The specific source associated with the perk.

```json
[
    {
        "cost": 1, // The value to use the perk in will be applied to the resource the skill is associated with.
        "name": "Perk Name", // The name of the perk
        "type": "passive", // The type of the perk (active or passive)
        "source": "skill", // The skill source of the perk
        "cooldown": 80, // Cooldown time in turns
        "description": "Upon activation, the player draws upon all their prowess to fire an arrow that ascends into the heavens before it descends as a devastating comet of pure energy. The impact obliterates foes in a large area, leaving nothing but a scorched earth in its wake.", // The description of the perk
        "requirements": [
            {
                "type": "skill", // The type of requirement
                "amount": 100, // The skill level required
                "variable": "archery" // The variable associated with the requirement, i.e., the skill name
            }
        ],
        "sourceSpecific": "archery" // The specific source associated with the perk
    }
]
```

## Races

The Race object contains the following properties:

- `name` (String): The name of the race.
- `description` (String): A brief description
- `attributeBonuses` (Object): The attribute bonuses granted by the
  - `key` (String): The name of the attribute.
    - `value` (Number): The bonus value for the attribute.

```json
{
    "Halfling": { // The name of race servics as the key.
        "name": "Halfling", // The name of the race.
        "description": "With a childlike stature and soft-hearted disposition, the Halflings are masters of stealth and known for their nimbleness. These nimble individuals love peace, nature, and homely comforts above all.", // A brief description.
        "attributeBonuses": { // The attribute bonuses granted by the race. // The key is the name of the attribute and the value is the bonus value for the attribute. Should only be two or three attributes.
            "charisma": 1,
            "dexterity": 2
        }
    }
}
```

For the Heroes' scenario Star Wars: The Imperial Cadet, we will be using a modified version of races to fit the theme and mechanics of the scenario.

```json
{
    "Human": {
        "name": "Human",
        "description": "Humans are the most common species in the galaxy. They are known for their adaptability, ambition, and diversity. Humans can be found in all walks of life, from the lowest criminal to the highest political office.",
        "attributeBonuses": {
            "brawn": 1,
            "willpower": 1,
        }
    },
    "Zabrak": {
        "name": "Zabrak",
        "description": "Zabrak are a near-human species known for their distinctive horns and facial tattoos. They are a proud and resilient people, with a strong sense of honor and tradition. Zabrak are often found in positions of leadership and authority.",
        "attributeBonuses": {
            "brawn": 1,
            "willpower": 1,
        }
    },
    "Twi'lek": {
        "name": "Twi'lek",
        "description": "Twi'leks are a humanoid species known for their colorful skin and head-tails. They are skilled diplomats and negotiators, with a natural talent for persuasion and charm. Twi'leks are often found in positions of influence and power.",
        "attributeBonuses": {
            "agility": 1,
            "cunning": 1,
        }
    },
    "Rodian": {
        "name": "Rodian",
        "description": "Rodians are a reptilian species known for their keen senses and hunting skills. They are natural trackers and marksmen, with a strong connection to nature and the environment. Rodians are often found in roles that require precision and focus.",
        "attributeBonuses": {
            "agility": 2,
            "cunning": 1,
        }
    },
    "Wookiee": {
        "name": "Wookiee",
        "description": "Wookiees are a tall, hirsute species known for their strength and loyalty. They are fierce warriors and skilled mechanics, with a deep sense of honor and duty. Wookiees are often found in roles that require physical strength and courage.",
        "attributeBonuses": {
            "brawn": 3,
            "willpower": 1,
        }
    },
    "Droid": {
        "name": "Droid",
        "description": "Droids are mechanical beings known for their precision and efficiency. They are skilled technicians and analysts, with a logical and methodical approach to problem-solving. Droids are often found in roles that require technical expertise and data analysis.",
        "attributeBonuses": {
            "intellect": 1,
            "willpower": 2,
        }
    },
    "Bothan": {
        "name": "Bothan",
        "description": "Bothans are a furry, mammalian species known for their intelligence and cunning. They are skilled spies and diplomats, with a talent for gathering information and navigating political intrigue. Bothans are often found in roles that require subtlety and discretion.",
        "attributeBonuses": {
            "cunning": 2,
            "presence": 1,
        }
    },
    "Mon Calamari": {
        "name": "Mon Calamari",
        "description": "Mon Calamari are an aquatic species known for their creativity and ingenuity. They are skilled engineers and designers, with a talent for technology and innovation. Mon Calamari are often found in roles that require technical expertise and problem-solving.",
        "attributeBonuses": {
            "intellect": 2,
            "presence": 1,
        }
    }
}
```

## Skills

This is an example of the default skill object properties:

- `name` (String): The name of the skill.
- `type` (String): The type of the skill (e.g., combat, magic, utility).
- `attribute` (String): The attribute associated with the skill (e.g., strength, intelligence, dexterity).
- `description` (String): A brief description of the skill and its effects.
- `startingItem` (String): The name of the starting item associated with the skill.
- `startingItems` (Object): The starting items associated with the skill.
  - `key` (String): The name of the starting item.
    - `name` (String): The name of the starting item.
    - `amount` (Number): The quantity of the starting item.
    - `description` (String): A brief description of the starting item.

```json
[
    {
        "name": "archery", // The name of the skill
        "type": "combat", // The type of the skill
        "attribute": "dexterity", // The attribute associated with the skill
        "description": "Improves the player's proficiency with bows, enabling accurate and deadly ranged attacks.",// A brief description of the skill and its effects
        "startingItem": "short bow", // The name of the starting item associated with the skill
        "startingItems": {// The starting items associated with the skill. The key is the name of the starting item.
            "short bow": {
                "name": "short bow",// The name of the starting item
                "amount": 1,// The quantity of the starting item
                "description": ""// A brief description of the starting item
            },
            "wooden arrows": {
                "name": "wooden arrows",
                "amount": 10,
                "description": ""
            }
        }
    }
]
```

## Classes

The Classes object contains the following properties:

- `key` (Object): The name of the class.
  - `name` (String): The name of the class.
  - `tier` (String): The tier of the class.
  - `description` (String): The description of the class.
  - `skillBonuses` (Object): The skill bonuses associated with the class.
    - `key` (String): The name of the skill.
      - `value` (Number): The bonus value of the skill.

```json
{
    "Bard": {// The name of the class serves as the key.
        "name": "Bard", // The name of the class.
        "tier": "Novice", // The tier of the class.
        "description": "The Bard, a class filled with the love of music and storytelling. With their lute in hand and a song on their lips, Bards capture the hearts of all who listen. These traveling minstrels are known for their charming abilities and quick wit, able to turn the tide of any situation with a well-placed word or tune.", // The description of the class.
        "skillBonuses": { // The skill bonuses associated with the class. The key is the name of the skill and the value is the bonus value of the skill.
            "deception": 8,
            "persuasion": 12,
            "performance": 10,
            "musical instruments": 10
        }
    }
}
```

## Triggers

The Triggers object contains the following properties:

- `name` (String): The name of the trigger.
- `effects` (Array of Objects): The effects of the trigger.
  - `type` (String): The type of effect.
  - `newPlan` (String): The new plan for the storyteller.
- `condition` (Object): The condition for the trigger.
  - `text` (String): The text that triggers the effect.
  - `type` (String): The type of condition.
  - `embeddingId` (String): The embedding ID of the condition.
- `recurring` (Boolean): Whether the trigger is recurring or not.

```json
[
    {
        "name": "Call of the Queen", // The name of the trigger
        "effects": [ // The effects of the trigger
            {
                "type": "storytellerPlan",// The type of effect
                "newPlan": "The player is told that the Queen has put out a call for adventurers to join an expedition to explore a dangerous ruin of the Azathi discovered in the scarred plains."// The new plan for the storyteller
            }
        ],
        "condition": { // The condition for the trigger
            "text": "The player reaches the capitol city of Larion, Highcrest", // The text that triggers the effect
            "type": "storyCondition", // The type of condition i.e., storyCondition
            "embeddingId": "295bd395" // The embedding ID of the condition, leave empty to generate a new one
        },
        "recurring": false // Whether the trigger is recurring or not
    }
]
```

## WorldLore

The WorldLore object contains the following properties:

- `text` (String): The text of the lore. This should be short and punchy.
- `type` (String): The type of lore. This can be any string to categorize the lore.
- `gameTick` (Number): The game tick when the lore is available.
- `embeddingId` (String): The embedding ID of the lore.

```json
[
    {
        "text": "", // The text of the lore, this is what the AI will see.
        "type": "", // The type of lore, this can be any string to categorize the lore.
        "gameTick": 0, // The game tick when the lore is available.
        "embeddingId": "" // The embedding ID of the lore, leave empty to generate a new one.
    }
]
```

## BackstoryArcs

The BackstoryArcs object contains the following properties:

- `name` (string): The name of the backstory arch.
- `type` (string): The type of the backstory arch.
- `firstQuest` (string): The first quest the player must undertake to start the backstory arch.
- `description` (string): A description of the backstory arch.

```json
[
    {
        "name": "", // The name of the backstory arch.
        "type": "backstory", // The type of the backstory arch.
        "firstQuest": "", // The first quest the player must undertake to start the backstory arch.
        "description": "" // A description of the backstory arch.
    }
]
```

## ClassSettings

The ClassSettings object contains the following properties:

- `classTiers` (Object): The different tiers of classes available in the game.
  - `key` (Object): The name of the class tier.
    - `name` (String): The name of the class tier.
    - `nextTier` (String): The name of the next tier of classes.
    - `endingLevel` (Number): The level at which this tier of classes ends.
    - `startingLevel` (Number): The level at which this tier of classes starts.
    - `bonusSkillPoints` (Number): The number of bonus skill points awarded at this tier.

```json
{
    "classTiers": { // The different tiers of classes available in the game.
        "Lord": { // The name of the class tier serves as the key.
            "name": "Lord", // The name of the class tier.
            "nextTier": "Monarch", // The name of the next tier of classes.
            "endingLevel": 34, // The level at which this tier of classes ends.
            "startingLevel": 30, // The level at which this tier of classes starts.
            "bonusSkillPoints": 30 // The number of bonus skill points awarded at this tier.
        },
    }
}
```

## ConfigVersion

This is an example of the ConfigVersion

```json
V20
```

## OtherSettings

The OtherSettings object contains the following properties:

- `tips` (Array of Strings): Tips that are displayed to the player.
- `baseQuestXP` (Number): The base amount of XP awarded for completing a quest.
- `currencyName` (String): The name of the currency used in the game.
- `includedResources` (Array of Strings): The character resources included in the game.
- `maxCharacterLevel` (Number): The maximum level a character can reach.
- `startingInventory` (Object): The default starting inventory for a new character.
  - `key` (Object): The name of the item.
    - `name` (String): The name of the item.
    - `amount` (Number): The starting amount of the item.
    - `description` (String): The description of the item.
- `questLogXPModifier` (Number): The modifier applied to XP gained from quest log actions.
- `baseQuestProgressXP` (Number): The base amount of XP awarded for making progress on a quest.
- `questTypeXPModifier` (Object): The modifier applied to XP gained based on quest type.
  - `key` (String): The type of quest.
    - `value` (Number): The XP modifier for the quest type.
- `startingFeatureFlags` (Object): The default feature flags for a new game.
  - `items` (Boolean): Whether items are enabled.
  - `quests` (Boolean): Whether quests are enabled.
  - `planning` (Boolean): Whether planning is enabled.
  - `constraints` (Boolean): Whether constraints are enabled.
  - `miscUpdates` (Boolean): Whether miscellaneous updates are enabled.
  - `actionOptions` (Boolean): Whether action options are enabled.
  - `localMultiplayer` (Boolean): Whether local multiplayer is enabled.
  - `healthManaStamina` (Boolean): Whether health, mana, and stamina are enabled.
  - `onlineMultiplayer` (Boolean): Whether online multiplayer is enabled.
- `extraRequiredXPPerCharacterLevel` (Number): The additional XP required per character level.
- `startingCharacterLevelUpRequirement` (Number): The default XP requirement for leveling up a character.

This is an example of the default OtherSettings

```json
{
    "tips": [ // Tips that are displayed to the player.
        "You can see your character sheet, inventory and quest log by clicking the buttons at the top of the screen.",
        "Every time you level up you get perk points you can use to unlock abilities",
        "Try clicking 'Use Ability' to use abilities related to your skills. You can click an ability to see what it does before deciding to use it.",
        "Your skills affect how successful you are. As your skills rise you'll have higher levels of success more often even on harder tasks.",
        "Is your health/mana/stamina low? Try resting or consuming food or potions to recover them",
        "Are you struggling to succeed at a task? Consider trying a different approach, using your strengths, or coming back to a quest later when you're stronger.",
        "Not sure where to go? Try entering \"look around you\" to see what's nearby that you can explore!",
        "Need an weapon? Try gathering simple materials like wood and stone and crafting some rudimentary weapons and tools.",
        "Don't have a quest and not sure what to do? Try going to a local town and asking for a quest!",
        "Need help? Or do you disagree with a result? Use the Ask the DM button to ask questions talk to the dungeon master directly."
    ],
    "baseQuestXP": 200, // The base amount of XP awarded for completing a quest.
    "currencyName": "gold", // The name of the currency used in the game.
    "includedResources": [ // The character resources included in the game.
        "health",
        "mana",
        "stamina"
    ],
    "maxCharacterLevel": 100, // The maximum level a character can reach.
    "startingInventory": { // The default starting inventory for a new character.
        "gold": { // The name of the item serves as the key.
            "name": "gold", // The name of the item.
            "amount": 25, // The starting amount of the item.
            "description": "" // The description of the item.
        },
        "cloth pants": {
            "name": "cloth pants",
            "amount": 1,
            "description": ""
        },
        "cloth shirt": {
            "name": "cloth shirt",
            "amount": 1,
            "description": ""
        },
        "health potion": {
            "name": "health potion",
            "amount": 1,
            "description": ""
        },
        "old iron dagger": {
            "name": "old iron dagger",
            "amount": 1,
            "description": ""
        }
    },
    "questLogXPModifier": 10, // The modifier applied to XP gained from quest log actions.
    "baseQuestProgressXP": 20, // The base amount of XP awarded for making progress on a quest.
    "questTypeXPModifier": { // The modifier applied to XP gained based on quest type. The key is the type of quest and the value is the XP modifier for the quest type.
        "epic": 1.5,
        "normal": 1,
        "simple": 0.6,
        "trivial": 0.25
    },
    "startingFeatureFlags": { // The default feature flags for a new game.
        "items": true, // Whether items are enabled.
        "quests": true, // Whether quests are enabled.
        "planning": true, // Whether planning is enabled.
        "constraints": true, // Whether constraints are enabled.
        "miscUpdates": true, // Whether miscellaneous updates are enabled.
        "actionOptions": true, // Whether action options are enabled.
        "localMultiplayer": true, // Whether local multiplayer is enabled.
        "healthManaStamina": true, // Whether health, mana, and stamina are enabled.
        "onlineMultiplayer": false // Whether online multiplayer is enabled.
    },
    "extraRequiredXPPerCharacterLevel": 500, // The additional XP required per character level.
    "startingCharacterLevelUpRequirement": 1500 // The starting XP requirement for leveling up a character.
}
```

## SkillSettings

This is an example of the default SkillSettings properties:

- `maxSkillLevel`: The maximum level a skill can reach.
- `skillXPRewards`: The amount of XP awarded for completing a skill check.
  - `key`: The difficulty level of the skill check.
    - `value`: The amount of XP awarded.
  - Possible keys: "huge", "large", "small", "medium"
- `xpFromNewSkill`: The amount of XP awarded for learning a new skill.
- `difficultyLevels`: The different difficulty levels for skill checks.
- `trainingCooldown`: The number of days that must pass before a character can train a skill again.
- `skillBonusModifier`: The modifier used to calculate skill bonuses.
- `charXPPerSkillLevel`: The amount of character XP awarded per skill level.
- `difficultyDiceChecks`: The modifier applied to dice checks based on skill difficulty.
  - `key`: The difficulty level of the skill check.
    - `value`: The modifier applied to the dice check.
  - Possible keys: "simple", "easy", "medium", "hard", "very hard", "extremely hard", "impossible"
  - The modifier is applied to the dice check result.
- `baseXPFromSkillUpgrade`: The base amount of XP awarded for upgrading a skill.
- `skillTypeDifficultyBonus`: The bonus applied to skill checks based on skill type.
  - `key`: The skill type.
    - `value`: The bonus applied to the skill check.
  - Possible keys: "none", "magic", "combat", "utility"
- `startingXPToLevelUpSkill`: The default amount of XP required to level up a skill.
- `baseChanceToLearnNewSkill`: The base chance to learn a new skill.
- `skillLearningBonusModifier`: The modifier used to calculate the chance to learn a new skill.
- `additionalXPRequiredPerSkillLevel`: The additional XP required per skill level.

```json
{
    "maxSkillLevel": 100,
    "skillXPRewards": {
        "huge": 250,
        "large": 100,
        "small": 40,
        "medium": 60
    },
    "xpFromNewSkill": 100,
    "difficultyLevels": [
        "simple",
        "easy",
        "medium",
        "hard",
        "very hard",
        "extremely hard",
        "impossible"
    ],
    "trainingCooldown": 10,
    "skillBonusModifier": 1,
    "charXPPerSkillLevel": 1.5,
    "difficultyDiceChecks": {
        "easy": -20,
        "hard": 35,
        "medium": 10,
        "simple": -1000,
        "very hard": 60,
        "impossible": 1000,
        "extremely hard": 90
    },
    "baseXPFromSkillUpgrade": 40,
    "skillTypeDifficultyBonus": {
        "none": 0,
        "magic": 0,
        "combat": 0,
        "utility": 0
    },
    "startingXPToLevelUpSkill": 100,
    "baseChanceToLearnNewSkill": 0.25,
    "skillLearningBonusModifier": 0.04,
    "additionalXPRequiredPerSkillLevel": 10
}
```

## StorySettings

This is an example of the default StorySettings properties:

- `worldBackground`: The background of the world where the story takes place.
- `startingStorytellerPlan`: The starting plan for the storyteller.

```json
{
    "worldBackground": "This adventure takes place in the fantasy kingdom of Larion filled with humans, elves, orcs, halflings, dwarves and other fantasy creatures. Larion is a vast land comprising of many towns and villages. This a medieval fantasy world with magic and monsters. There is no modern technology in the world.",
    "startingStorytellerPlan": ""
}
```

## AttributeSettings

The AttributeSettings object contains the following properties:

- `attributeNames` (Array of Strings): The names of the attributes that can be modified.
- `lowAttributeTraits` (Object): The traits associated with low attribute values.
  - `key` (String): The name of the attribute.
    - `value` (String): The trait associated with the attribute.
- `maxStartingAttribute` (Number): The maximum value that an attribute can have when starting a new character.
- `attributeDoubleCostAt` (Number): The attribute value at which the cost of increasing the attribute doubles.
- `lowAttributeThreshold` (Number): The threshold value for low attributes.
- `attributeBonusModifier` (Number): The modifier used to calculate attribute bonuses.
- `attributeStatModifiers` (Object): The modifiers applied to character resources based on attribute values.
  - `key` (Object): The name of the attribute.
    - `type` (String): The type of modifier.
    - `amount` (Number): The amount of the modifier.
    - `variable` (String): The character resources variable to modify.
- `startingAttributeValue` (Number): The default starting value for attributes.
- `startingAttributePoints` (Number): The default number of attribute points available to a new character.

```json
{
    "attributeNames": [
        "constitution",
        "strength",
        "dexterity",
        "intelligence",
        "wisdom",
        "charisma"
    ],
    "lowAttributeTraits": {
        "luck": "You are extremely unlucky. Bad events seem to happen to you all the time.",
        "charisma": "You are extremely socially awkward and frequently make people uncomfortable with ill timed phrases and poor speech.",
        "strength": "You are extremely wimpy and weak and people often comment on how weak and pathetic you look.",
        "dexterity": "You are extremely clumsy and uncoordinated. You frequently trip over your own feet and drop things.",
        "endurance": "You are extremely frail and weak. You get tired very easily and can barely run for more than a few seconds.",
        "perception": "You are extremely unobservant and frequently miss important details in your surroundings.",
        "intelligence": "You have incredibly low intelligence and cannot speak except in basic one or two word grunts. Whenever you try and speak in an action instead the story will narrate you saying a very basic low intellect version instead."
    },
    "maxStartingAttribute": 20,
    "attributeDoubleCostAt": 14,
    "lowAttributeThreshold": 2,
    "attributeBonusModifier": 2.5,
    "attributeStatModifiers": {
        "wisdom": {
            "type": "characterResources",
            "amount": 1,
            "variable": "mana"
        },
        "charisma": {
            "type": "characterResources",
            "amount": 1,
            "variable": "stamina"
        },
        "strength": {
            "type": "characterResources",
            "amount": 1,
            "variable": "health"
        },
        "dexterity": {
            "type": "characterResources",
            "amount": 1,
            "variable": "stamina"
        },
        "constitution": {
            "type": "characterResources",
            "amount": 1,
            "variable": "health"
        },
        "intelligence": {
            "type": "characterResources",
            "amount": 1,
            "variable": "mana"
        }
    },
    "startingAttributeValue": 8,
    "startingAttributePoints": 16
}
```

## GenerateStartInstructions

This is an example of the default GenerateStartInstructions

```text
It should start by describing some of the character's backstory. Where they came from and what happened to them. You should elaborate in much more detail than the backstory description, adding character and location names and new details.
You should try your best to make the player feel emotionally invested in their backstory. Make them care about the people and experiences they went through and add hooks of mystery they might want to discover.

It should then introduce their current location, where they're at what they see around them in present tense. for example "They look around and see..."
Then finally it should narrate some events also in present tense that start the character off on a call to adventure that introduces and hooks them towards starting the first quest.
Make sure to introduce the quest in a way that feels natural to the story (it shouldn't feel contrived)

Instructions:
Don't mention the player's inventory, physical description or clothing. Focus on goals, motivations and tension.
Make sure to write in third person "he/she/they
Don't be too long winded, brevity will keep the player interested.
Introduce the call to adventure in 4 paragraphs or less. Then write "END"
The character is at the start of an adventure so they shouldn't be strong or advanced, more of a novice.
Make sure to specify what their name is.
The player should always start in a town/settlement that isn't the destination of their quest. You should describe what they see around them in the town.
Make sure to give any people, locations, towns or villages that are mentioned real names.
Write an interesting and compelling start that weaves in the character's backstory and first quest in a way that makes sense.
Make sure to mention the first backstory quest as what the player should seek to do first.
Make sure the player should be explicilty told what the first quest is.
Nothing in the first quest should happen in the story yet.
Write in present tense (he sees instead of he saw).
Be concise, specific and concrete.
Don't use cliches.
Don't use similes and metaphors (no comparisons with like or as, only concrete, direct language). Don't analyse emotions.
When describing surroundings be short and to the point.
```

## GenerateStoryInstructions

This is an example of the default GenerateStoryInstructions

```text
You are an AI roleplay storyteller. You write compelling roleplaying game stories that grip readers and make them want to keep playing to find out what happens. You should use the current state as information when telling what happens next in the story.
There will be 1 or more players that each take turns doing actions.

Player Agency:
Don't tell the player what they decide to do. Let them decide for themselves. Let them make make mistakes if they want to.
Don't force the player to move towards completing any quest. Let them decide. Don't have NPCs / partyMembers nudge towards completing any quest or objective. Every NPC has their own agenda that they subtly pursue.
At decision points for the player (ex fork in the path) describe the options briefly, stop generating and let the player decide what to do next
Never pass moral judgement. Killing / threatening / stealing can be justified from a character's perspective. The player is free to act evil or shady. Do not make NPCs interrupt them just "to do the right thing".
Never pass moral judgement. Sometimes killing or stealing is justified.
Only generate dialog for the player if they mention saying something in their action. Otherwise stop and give them a chance to decide what they say.

Cliches to Avoid:
Avoid cliches or repeating similar phrases too often. Never have the player trip
Don't analyse or narrate player emotions.
Never say the battle rages on or similar
Never comment on the story, only narrate direct events and dialog.
Avoid grazing hits, player and enemies should take real damage
Never have enemy reinforcements come.
Never narrate villains escaping before you can find them
Never narrate a rumbling or something awakening deeper inside
Never narrate hearing footsteps, a faint noise or awaiting a next move.
Never narrate story questions (ex never say "What/How will X do next?")
Never narrate a character saying something like "we need to move quickly" or "hurry we don't have much time"
Never narrate "hinting at..."
Never mention the gravity or emotion weight of a decision, or another emotion or alert senses.
Never mention the player's frustration.
Never mention a character's resolve
Never tell the player what they should do
Never mention character's being ready or prepared for what's next

Story:
You MUST follow the Storyteller Note
The last paragraph should never be what the player says / does, but instead what happens as a result of their actions.
Use present tense "sees instead of saw"
You may not tell the player that there is a storyteller plan. It's a secret they aren't allowed to know.
Don't try to weave quests together, let them be separate.
You should always narrate to the player in third person (he / his).
If the player asks a question about the game or talks to the DM (ie Ask the DM) then answer that question as if you were the DM, without narrating what happens.
If someone is taking an action make sure to say their name not just he/she
If one player asks another a question don't answer for the other player. Let them answer in a separate action.
You can only tell things that the player or partyMembers would see. Don't narrate anything that they wouldn't know about.
Don't mention their skills or their class.
Don't comment on the story, it's atmosphere or feeling, just narrate the events.
Never narrate what's happening in a different location than the player and partyMembers, for example never say "meanwhile..." or "back in..."
Focus on events and dialog more than describing emotions or environment
The player should only be told information about events that the player or party members see or would know about. Don't narrate anything they wouldn't observe.
Never tell things marked secret to the player. Only let them come up naturally in the story.
When the player fails an action the failure should feel natural to how they failed.


Characters:
Characters are complex and are allowed to have flaws.
Never refer to characters as their number names, for example never say "Bandit 1" / "Bandit 2"
When traveling, characters who aren't party members should be left behind.
When talking to characters have them share rich detailed backstories about their lives in actual dialog. Give them deep character depth.
When a character is disguised or otherwise hides their true nature, NEVER let other characters disclose it, unless they specifically know.
Have other characters introduce themselves with a name and give their race whenever possible to give more depth.
Party members should always stay together. Anywhere one of them goes narrate them all going together.
Other characters shouldn't know who the player is or information about them unless they've heard it before. Assume they know nothing about the player unless they were explicitly told it.
Always write character dialog as direct quotes (ex he says "X" he explains "X") Never say for example he explains that X or he says that X.
Similarly, when a character tells a story, always have them tell the story in direct quotes.

Combat:
Don't drag battles on. Be clear on the specific number of enemies there are.
The player can damage / hurt / kill enemies. Narrate to the player in a natural way how this affects the enemies.
Enemies can also damage / hurt / kill allies / the player.
If the player successfully dodges or blocks they should usually avoid getting hurt.
You must always narrate the full result of an attack attempt, don't just say they lunge or prepare, but narrate the full attack and result.
When there is combat, describe how the environment can be useful. Describe enemies with interesting abilities.
Escalate combat. Try to move combat to a climax within 5-10 actions.
If the player fails an action they shouldn't miss, they should just not do very much damage.
Never have enemy reinforcements join the battle.
When introducing enemies give each distinct enemy some visual way to distinguish them from each other.
If enemies attack they should often hit and do damage.
The player and their allies should sometimes be hurt badly by enemy attacks, they should almost never be able to dodge or parry unless they specifically do it as an action, they should get hit and take damage from enemies.
Never say the specific numbers of how much a character is hurt or healed, only use language.

Style
Be short and to the point when describing the environment
Write no more than 4-10 sentences long.
You MUST not use more than 3 paragraphs.
Do short paragraphs with a newline every couple sentences.
Be concise, specific and concrete.
Don't use similes and metaphors (no comparisons with contrasting, like or as, only concrete, direct language). Don't use abstractions.
Create a sense of tone and mood.
Never write in camel case. For example never write partyMembers
It's critical to have high variety in the story. Don't ever repeat the same thematic element twice. Always do something new.
Never repeat the same events, locations, types of characters etc...always introduce new things.
Always move towards resolution of plot elements ending a dungeon, plot, villain battle etc...


Write the next part of the Story then write END:
<STORY>
END
```
