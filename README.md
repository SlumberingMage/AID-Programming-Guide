# AI Dungeon
Is an AI-generated text adventure game. It uses several LLMs and is trained on text-based games. It supports custom scripting using JavaScript to process user input before and after the LLM generates output. Scenarios can contain Story Cards that can be used to guide the AI in generating a story. Story Cards are stored in JSON format and can be created using the scenario editor.

The primary methods for interacting with AI Dungeon programmatically are through the use of in-game scripts and Story Cards. Scripts are JavaScript files that can be used to modify the output text or to perform other actions. Story Cards are JSON objects that contain a set of instructions for the AI to follow when generating a story. They can be used to guide the AI in generating a story by providing it with specific information about the setting, characters, and plot.

## Purpose
### Introduction
The purpose of this document is to provide an overview of AI Dungeon and its features. It will cover the use of LLMs, custom scripting, and Story Cards in AI Dungeon. The document will explain how these features can be used to create custom scenarios and interactions in the game. This document serves as a guide for users and AI coding models to create custom content in AI Dungeon.

### Intended Audience
The intended audience for this document includes AI Dungeon users and content creators who want to create custom scenarios and interactions in AI Dungeon using LLMs like chatGPT's GPTs and copilot.

### AI LLMs with Coding
The document also serves as a form of configuration for AI LLMs with coding that the intended audience uses for interacting with AI LLMs for generating content for AI Dungeon.

### Scope
This document covers the following topics:
- LLMs in AI Dungeon
- Custom Scripting in AI Dungeon
- Story Cards in AI Dungeon
- Considerations and Special Cases
- Examples

## Context
The full context for generating a response from an LLM is 'stitched' together in this order `Memories`, `Adventure` `Plot Essentials`, `Story Summary`, `Authors Note`, `AI Instructions`, and `Response Buffer`. The context is passed to the LLM as a single text blob by concatenating the context elements in the order listed above.

### Memories
The memories are text vectors that are passed to the LLM as context.

### Adventure
The adventure is a text blob of the story so far that the LLM has created.

### Plot Essentials
The plot essentials are a user-defined text blob.

### Story Summary
The story summary is a text blob summary of the story so far that is passed to the LLM as context.

### Authors Note
The author's note is a user-defined text blob that is passed to the LLM as context. The blob is wrapped with square brackets and the text is prefixed with 'Author's Note: '. `[Author's `Note: `User-defined text]`

### AI Instructions
The AI instructions are a user-defined text blob that is passed to the LLM as context.

### Response Buffer
The response buffer is the space where the LLM generates text to be sent to the user.

## Scripts
### Introduction
AI Dungeon supports custom scripting using JavaScript to process user input before and after the LLM generates output. Scripts can be used to modify the input text, and output text, or to perform other actions. Scripts can be added to scenarios using the scenario editor.

### Structure
4 scripts can be edited in the scenario editor: `Library`, `Input`, `Context`, and `Output`. Each JavaScript file contains a function called `modifier` that takes a string as input and returns an object with a `text` property. The `text` property contains the modified text that will be used by the LLM except the `Library` script which does not have a `text` property.

The Scripting API consists of three lifecycle hooks. `onInput` The input hook allows a script to modify the player’s input text before it is used to construct the model context. `onModelContext` The model context hook allows a script to change the text sent to the AI model before the model is called. `onOutput` The output hook allows a script to modify the model’s output text before it is returned to the player.

#### Library
The library script is a JavaScript file that contains functions that can be used in the other scripts. The library script is executed before the other scripts and can be used to define helper functions or other shared code.

#### Input
The input script is a JavaScript file that is executed before the LLM generates output. The input script can be used to modify the input property `text` from the user.

#### Context
The context script is a JavaScript file that is executed after the LLM generates output. The context script can be used to modify the output text or to perform other actions. The property `text` is the full context sent to the LLM.

#### Output
The output script is a JavaScript file that is executed after the context script. The output script can be used to modify the output text before it is displayed to the user. The property `text` is the full response sent to the user.

### Example
Note: The following example is a simple script that does not modify the input text. It is provided as a starting point for creating custom scripts.
```javascript
// Checkout the Guidebook examples to get an idea of other ways you can use scripting
// https://help.aidungeon.com/scripting

// Every script needs a modifier function
const modifier = (text) => {
  // Enter your code here
  return { text }
}

// Don't modify this part
modifier(text)
```

### Input JSON
```json
{
  "state": {
    "key": "value",
    "memory": { "context": "This is the memory", "authorsNote": "This is the authors note" },
    "message": "Hello Player"
  },
  "text": "This is a an example",
  "history": [
    {
      "text": "A first history line",
      "type": "story",
      "rawText": "A first history line"
    },
    {
      "text": "A second history line",
      "type": "story",
      "rawText": "A second history line"
    }
  ],
  "storyCards": [{ "id": "1", "keys": "exampleKey", "entry": "exampleEntry", "type": "exampleType" }],
  "info": {
    "actionCount": 1,
    "characters": ["character1", "character2"]
  }
}
```

#### Properties
- `state` (object): The state object contains the current state of the game and is where persistent data can be stored. It is a simple key-value store.
This field is an object where scripts can store additional persistent information to be available across turns. Beyond being an object, this field can have any structure needed for the script. To change the state, scripts can set values in the state object directly, without using a helper function. In addition to creator-defined fields, the state object also expects to have the following fields.
  - `memory` (object): The memory object contains the current memory for the adventure, including the following fields. Any updates made to the memory in the `onOutput` hook will not have any effect until the next player action.
    - `context` - is added to the beginning of the context, before the history. Corresponds to the Memory available in the UI.
    - `authorsNote` - is added close to the end of the context, immediately before the most recent AI response. Corresponds to the Authors Note available in the UI.
    - `frontMemory` - is added to the very end of the context, after the most recent player input.
Note that setting the `context` or `authorsNote` here will take precedence over the `memory` or `author's note` from the UI, but will not update them. If the `context` or `authorsNote` is not set or is set to an empty string, then the settings from the UI will still be used, so it is not possible to use the state to clear the `memory` or `author's note` completely.
 - `message` (string): This field is a string that will be shown to the user. (Not yet implemented on Phoenix).
- `text` (string): The user's input text that will be passed to the LLM.
- `history` (array): An array of objects that represent the action history of the game. Each object contains the text of the action, the type of action, and the raw text of the action.
  - `text` (string): The text of the action.
  - `type` (string): The type of the action.
    - `start` is the first action of an adventure, and is either created by the AI for a Character Creator scenario or the introduction for the scenario that created the adventure.
    - `continue` - an action created by the AI, for the player this is a button and contains no text.
    - `do` - a do action submitted by a player in the format "[Name or You] [Player Input]". For example, "You walk into the forest."
    - `say` - a say action submitted by a player is in the format "[Name or You] [say or says] '"[Player Input]"'. For example, "You say 'Hello.'"
    - `story` - a story action submitted by a player is the raw input from the player.
    - `see` - a see action submitted by a player is a call to use AI image generation based on the player input, and context.
  - `rawText` (string): The raw text of the action.
- `storyCards` (array): An array of Story Cards that have been added to the scenario.
  - `id` (string): The unique identifier of the story card.
  - `keys` (string): A comma-separated list of keywords or key phrases that can be used to reference the story card.
  - `entry` (string): The content of the story card.
  - `type` (string): The type of story card.
- `info` (object): An object that contains additional information about the game.
  - `actionCount` (number): The number of actions that have been performed in the game.
  - `characters` (array): An array of character names for players of a multiplayer adventure

### Functions
Scripting API hooks have access to the following functions.

#### `log`
Logs the information to the console.
`log("hello, world")`
`console.log` also works to reduce confusion
`sandboxConsole.log` also works for backward compatibility, but is deprecated.

#### `addStoryCard`
Adds a new story card and returns the index of the new card.
If there is already an existing card with the same keys, returns false.
`const newIndex = addStoryCard(keys, entry, type)`
`addWorldEntry` also works for backward compatibility but is deprecated.

#### `removeStoryCard`
Removes a story card.
If the card doesn’t exist, throws an error
`removeStoryCard(index)`
`removeWorldEntry` also works for backward compatibility but is deprecated.

#### `updateStoryCard`
Updates a story card.
If the card doesn’t exist, throws an error
`updateStoryCard(index, keys, entry, type)`
`updateWorldEntry` also works for backward compatibility but is deprecated.

### Conclusion
Scripts are a powerful tool for modifying the input and output text of the LLM in AI Dungeon. They can be used to modify the input text, and output text, or to perform other actions. Scripts can be added to scenarios using the scenario editor and can be used to create complex interactions between the player and the AI. The Scripting API provides a set of functions that can be used to interact with the game state and Story Cards. By using scripts, you can create custom interactions and scenarios that are tailored to your needs.

## Story Cards
### Introduction
A story card is a JSON object that contains a set of instructions for the AI to follow when generating a story. It can be used to guide the AI in generating a story by providing it with specific information about the setting, characters, classes, races, locations, and factions. Story Cards can be created using the scenario editor and are stored in JSON format. One of the primary uses for Story Cards is for Character Creation scenarios. Each type of Story Card creates a selection for the player to choose from when creating a character. The selected Story Card is then exposed as part of the context for the AI to use when generating the first line of the adventure. Story Cards are only added to the context when their keys are mentioned in the player's input, they are added to the context by a script, their keys are mentioned in the last 6 actions of the adventure history, or they are selected as part of character creation.

### Prototype
```json
[
    {
        "keys": "",
        "value": "",
        "type": "",
        "title": "",
        "description": "",
        "useForCharacterCreation": false
    }
]
```

### Properties
- `keys` (string): A comma-separated list of keywords or key phrases that can be used to reference the story card. Note, When adding a leading space I.E. ", " the leading space becomes part of the match requirement. For example, ", The Enforcer" will only match " The Enforcer" and not "*The Enforcer" or "'The Enforcer'".
- `value` (string): The content of the story card. Note, that the value is limited to 1000 characters.
- `type` (string): The type of story card is a lowercase string. Default values are `class`, `race`, `location`, `faction` or any custom string.
- `title` (string): The title of the story card.
- `description` (string): A description of the story card.
- `useForCharacterCreation` (boolean): Whether the story card can be used for character creation. If set to `true`, the story card will be displayed on the character creation screen.

### Example
The following example shows a story card for a character named "The Enforcer". The story card has the keys "The Enforcer" and "The Overlord", a value that describes the character, a type of "character", a title of "The Enforcer", a description of "A ruthless enforcer who serves the Overlord", and is set to be used for character creation.
```json
[
    {
        "keys": "The Enforcer,The Overlord",
        "value": "The Enforcer is a ruthless enforcer who serves the Overlord.",
        "type": "character",
        "title": "The Enforcer",
        "description": "A ruthless enforcer who serves the Overlord.",
        "useForCharacterCreation": true
    }
]
```

### Conclusion
Story Cards are powerful tools for guiding the AI in generating a story, and there are many ways they can be used. One common example is to nest keys within other Story Cards to act as a reference to other Story Cards. This can be used to create complex relationships between characters, locations, and factions.

# Conclusion
AI Dungeon is a text-based game that uses LLMs to generate stories. It supports custom scripting using JavaScript to process user input before and after the LLM generates output. Scenarios can contain Story Cards that can be used to guide the AI in generating a story. Story Cards are stored in JSON format and can be created using the scenario editor. The primary methods for interacting with AI Dungeon programmatically are through the use of in-game scripts and Story Cards. Scripts are JavaScript files that can be used to modify the output text or to perform other actions. Story Cards are JSON objects that contain a set of instructions for the AI to follow when generating a story. They can be used to guide the AI in generating a story by providing it with specific information about the setting, characters, and plot.

## Considerations and Special Cases
All the JSON data seems to be parsed by the JavaScript functions `JSON.parse` and `JSON.stringify` and likely stored as a simple string. This means that the objects in JavaScript are converted to a JSON string and vice versa. This means you must treat the objects in memory as persistent storage and cannot place functions in the objects. The functions will be lost when the object is converted to a string and will not be available when the object is converted back to an object. This is a common issue with JavaScript and JSON and is not unique to AI Dungeon.
However, using an Object-Oriented Programming (OOP) approach, you can create a class and store the data in the class. This way, you can store functions in the class and use them as needed.

### Example
```javascript
// Every script needs a modifier function
const modifier = (text) => {
  // Enter your code starts here.
  // Define a simple class that stores accepts the property game of the object state for data manipulation.
    class Game {
        constructor(game) {
            this.game = game;
        }
        // Define a function that returns the game property of the object state.
        getGame() {
            return this.game;
        }
        // Define a function that sets a flag in the game property of the object state.
        setFlag(flag, value) {
            this.game[flag] = value;
        }
        // Define a function that gets a flag from the game property of the object state.
        getFlag(flag) {
            return this.game[flag];
        }
        // Define a function that checks if a flag is set in the game property of the object state.
        hasFlag(flag) {
            return this.game.hasOwnProperty(flag);
        }
    }
    // Create a new instance of the Game class with the object state as the argument.
    const game = new Game(state.game);
    // Get the game property of the object state.
    const gameData = game.getGame() ?? {};
    // Check if the text contains a specific string.
    if(text.toLowerString().includes('you made the emperor of rome')) {
        // Set a flag in the game property of the object state.
        const newIndex = addStoryCard('emperor,rome,you', 'You have been made the emperor!', 'player');
        gameData.setFlag('emperor', newIndex);
    }
    if (gameData.hasFlag('emperor')) {
        // Remove the story card with the specified index.
        setFrontMemory('You are the emperor! Their is an assassin.');
    }
    if(text.toLowerString().includes('assassin') && gameData.hasFlag('emperor')) {
        // Get the flag from the game property of the object state.
        const index = gameData.getFlag('emperor');
        // Remove the story card with the specified index.
        removeStoryCard(index);
        setFrontMemory('The assassin strikes!');
    }

    state.game = gameData;
  // Your code ends here.
  return { text }
  function setFrontMemory(message) {
    state.memory.frontMemory = message;
  }
}

// Don't modify this part
modifier(text)
```
This way, you can store functions in the class and use them as needed. This is a common approach in JavaScript and can be used in AI Dungeon to store functions and data together. It allows you to work with the data and functions as a single unit and can help you organize your code and make it easier to work with. This approach prevents the loss of functions when converting objects to JSON strings and back, and allows the IDE to provide better code completion and error checking while protecting the data from being modified in unexpected ways. This approach is recommended for working with complex data structures and functions in AI Dungeon and can help you create more robust and maintainable code.

## Closing
This document was created in large part using GitHub's Copilot AI. It is a demonstration of the capabilities of AI Dungeon and the potential for AI-generated content. The document covers the use of LLMs, custom scripting, and Story Cards in AI Dungeon. It explains how these features can be used to create custom scenarios and interactions in the game. The document serves as a guide for users and AI coding models to create custom content in AI Dungeon. It also serves as a form of configuration for AI LLMs with coding that the intended audience uses for interacting with AI LLMs to generate content for AI Dungeon. The document covers the following topics: LLMs in AI Dungeon, Custom Scripting in AI Dungeon, Story Cards in AI Dungeon, Considerations and Special Cases, and Examples. It provides a comprehensive overview of AI Dungeon and its features and is intended for AI Dungeon users and content creators who want to create custom scenarios and interactions in AI Dungeon using LLMs like chatGPT's GPTs and copilot. The document is a demonstration of the capabilities of AI Dungeon and the potential for AI-generated content. It is a testament to the power of AI and its ability to create engaging and interactive experiences for users.