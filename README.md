# AI Dungeon

Is an AI-generated text adventure game. It uses several LLMs and is trained on text-based games. It supports custom scripting using JavaScript to process user input before and after the LLM generates output. Scenarios can contain Story Cards that can be used to guide the AI in generating a story. Story Cards are stored in JSON format and can be created using the scenario editor.

The primary methods for interacting with AI Dungeon programmatically are through the use of in-game scripts and Story Cards. Scripts are JavaScript files that can be used to modify the output text or to perform other actions. Story Cards are JSON objects that contain a set of instructions for the AI to follow when generating a story. They can be used to guide the AI in generating a story by providing it with specific information about the setting, characters, and plot.

## Purpose

### Introduction

The purpose of this document is to provide an overview of AI Dungeon and its features. It will cover the use of LLMs, custom scripting, and Story Cards in AI Dungeon. The document will explain how these features can be used to create custom scenarios and interactions in the game. This document serves as a guide for users and AI coding models to create custom content in AI Dungeon.

### Intended Audience

The intended audience for this document includes AI Dungeon users and content creators who want to create custom scenarios and interactions in AI Dungeon using LLMs like chatGPT's GPTs and copilot.

## Scope

This document covers the following topics:

- Custom Scripting in AI Dungeon
- Story Cards in AI Dungeon
- Considerations and Special Cases
- Examples

## Context

The full context for generating a response from an LLM is 'stitched' together in this order `AI Instructions`,  `Plot Essentials`, `Story Cards`, `Story Summary` (if enabled), `Memories` (if enabled), `Recent Story`, `Author's Note`, and `Response Buffer`. The context is passed to the LLM as a single text blob by concatenating the context elements in the order listed above. It is important to note that the closer an element is to the end of the context, the more likely it is to be used by the LLM in generating a response.

### AI Instructions

AI Instructions tell the AI how to behave. They are either the default instructions for the current model, the default instructions for the scenario, or a variation customized by the user. These instructions are the first thing in context and have no prefix.

### Plot Essentials

Plot Essentials is a block of user-defined text that typically contains information about the main character or the current story. This text immediately follows the AI Instructions and has no prefix.

### Story Cards

Story Cards are relavant blocks of user-defined text that are chosen by the AI using a trigger-based system. Each card is separated by a blank line, and the section is prefixed with the heading `World Lore:`.

### Story Summary

Story Summary (if enabled) is a summary of the story so far. For premium users, this can be automatically updated by the AI. Otherwise, it is fully user-defined and updated manually. This text is separated by blank lines, and is prefixed with the heading `Story Summary:`.

### Memories

Memories (if enabled) are relavant summaries of past story events that are chosen by the AI using an embedding system. Each memory is separated by a newline (not a blank line), and the section is prefixed with the heading `Memories:`.

### Recent Story

Recent Story includes as many recent actions as the context will allow. This text is separated by blank lines, with each action displaying in the same paragraph format as it is shown to the user, with Do and Say actions being separated by blank lines and prefixed with `>`. This section is prefixed with the heading `Recent Story:`.

### Author's Note

The author's note is a block of user-defined text that is passed to the LLM as context. The text is wrapped with square brackets and the text is prefixed with 'Author's Note: '. `[Author's Note: user-defined-text]`

### Response Buffer

The response buffer is the space where the LLM generates text to be sent to the user.

## Scripts

### Scripts: Introduction

AI Dungeon supports custom scripting using JavaScript to process user input before and after the LLM generates output. Scripts can be used to modify the input text, and output text, or to perform other actions. Scripts can be added to scenarios using the scenario editor.

### Scripts: Structure

4 scripts can be edited in the scenario editor: `Library`, `Input`, `Context`, and `Output`. Each JavaScript file contains a function called `modifier` that takes a string as input and returns an object with a `text` property. The `text` property contains the modified text that will be used by the LLM except the `Library` script which does not have a `text` property.

The Scripting API consists of three lifecycle hooks. `onInput` The input hook allows a script to modify the player's input text before it is used to construct the model context. `onModelContext` The model context hook allows a script to change the text sent to the AI model before the model is called. `onOutput` The output hook allows a script to modify the model's output text before it is returned to the player.

#### Scripts: Library

The library script is a JavaScript file that contains functions that can be used in the other scripts. The library script is executed before the other scripts and can be used to define helper functions or other shared code.

#### Scripts: Input

The input script is a JavaScript file that is executed before the LLM generates output. The input script can be used to modify the input property `text` from the user.

#### Scripts: Context

The context script is a JavaScript file that is executed after the LLM generates output. The context script can be used to modify the output text or to perform other actions. The property `text` is the full context sent to the LLM.

#### Scripts: Output

The output script is a JavaScript file that is executed after the context script. The output script can be used to modify the output text before it is displayed to the user. The property `text` is the full response sent to the user.

### Scripts: Example

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

### Scripts: Input JSON

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

#### Scripts: Properties

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
    - `do` - a do action submitted by a player in the format "> \[Name or You] \[Player Input]". For example, "> You walk into the forest."
    - `say` - a say action submitted by a player is in the format "> \[Name or You] \[say or says] '"\[Player Input]"'. For example, "> You say 'Hello.'"
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

### Scripts: Functions

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

### Scripts: Conclusion

Scripts are a powerful tool for modifying the input and output text of the LLM in AI Dungeon. They can be used to modify the input text, and output text, or to perform other actions. Scripts can be added to scenarios using the scenario editor and can be used to create complex interactions between the player and the AI. The Scripting API provides a set of functions that can be used to interact with the game state and Story Cards. By using scripts, you can create custom interactions and scenarios that are tailored to your needs.

## Story Cards

### Story Cards: Introduction

A story card is a JSON object that contains a set of instructions for the AI to follow when generating a story. It can be used to guide the AI in generating a story by providing it with specific information about the setting, characters, classes, races, locations, and factions. Story Cards can be created using the scenario editor and are stored in JSON format. One of the primary uses for Story Cards is for Character Creation scenarios. Each type of Story Card creates a selection for the player to choose from when creating a character. The selected Story Card is then exposed as part of the context for the AI to use when generating the first line of the adventure. Story Cards are only added to the context when their keys are mentioned in the player's input, they are added to the context by a script, their keys are mentioned in the last 6 actions of the adventure history, or they are selected as part of character creation.

### Story Cards: Prototype

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

### Story Cards: Properties

- `keys` (string): A comma-separated list of keywords or key phrases that can be used to reference the story card. Note, When adding a leading space I.E. ", " the leading space becomes part of the match requirement. For example, ", The Enforcer" will only match " The Enforcer" and not "*The Enforcer" or "'The Enforcer'".
- `value` (string): The content of the story card. Note, that the value is limited to 1000 characters.
- `type` (string): The type of story card is a lowercase string. Default values are `class`, `race`, `location`, `faction` or any custom string.
- `title` (string): The title of the story card.
- `description` (string): A description of the story card.
- `useForCharacterCreation` (boolean): Whether the story card can be used for character creation. If set to `true`, the story card will be displayed on the character creation screen.

### Story Cards: Example

The following example shows a story card for a character named "The Enforcer". The story card has the keys "The Enforcer" and "The Overlord", a value that describes the character, a type of "character", a title of "The Enforcer", a description of "A ruthless enforcer who serves the Overlord", and is set to be used for character creation.

```json
[
    {
        "keys": "The Enforcer,The Overlord",
        "value": "The Enforcer is a ruthless enforcer who serves the Overlord.",
        "type": "character",
        "title": "The Enforcer",
        "description": "A ruthless enforcer who serves the Overlord.",
        "useForCharacterCreation": false
    }
]
```

### Story Cards: Conclusion

Story Cards are powerful tools for guiding the AI in generating a story, and there are many ways they can be used. One common example is to nest keys within other Story Cards to act as a reference to other Story Cards. This can be used to create complex relationships between characters, locations, and factions.

## String Interpolation

AI Dungeon supports string interpolation in the form of `${}`. This allows you to insert variables, expressions, or functions into a string. String interpolation can be used in the input, output, and context scripts to modify the text that is passed to the LLM or displayed to the user. It's key to understand that whatever is inside the `${}` is displayed as a string to the users. As an example, `You are ${Describe yourself at the cursor. "You are |." I.E. "Eric, a man in his 30s and passionate about ecology"}.` would be displayed as `Describe yourself at the cursor. "You are |." I.E. "Eric, a man in his 30s and passionate about ecology` to the user. This string can be used in the story, plot essentials, and author's note. Please note that the change of any character in the string will create another entry for the player. This is of concern when switching between mobile and desktop devices. As an example, mobile devices use a different character for the single and double quote character than desktop devices do. I.E. mobile devices use `“` and `”` for double quotes and `‘` and `’` for single quotes. Desktop devices use `"` and `'` for double and single quotes respectively.

## Considerations and Special Cases

All the JSON data seems to be parsed by the JavaScript functions `JSON.parse` and `JSON.stringify` and likely stored as a simple string. This means that the objects in JavaScript are converted to a JSON string and vice versa. This means you must treat the objects in memory as persistent storage and cannot place functions in the objects. The functions will be lost when the object is converted to a string and will not be available when the object is converted back to an object. This is a common issue with JavaScript and JSON and is not unique to AI Dungeon.
However, using an Object-Oriented Programming (OOP) approach, you can create a class and store the data in the class. This way, you can store functions in the class and use them as needed.

## UX/UI

UX/UI stands for User Experience/User Interface. It is the design of the interface that the user interacts with. They are not the same thing, but they are closely related. UX is about the experience the user has when interacting with the interface, while UI is about the design of the interface itself. In AI Dungeon the UI is defined, and you cannot change it, the UX is largely defined as well. However you control several things to do with UX. The creation of string interpolation, the selection of story cards, and the creation of scripts. These are all ways to control the UX of the game.

### Principles of UX

- **Usability**: Make sure the interface is easy to use and understand.
- **Simplicity**: Keep the interface simple and easy to use.
- **Consistency**: Keep the interface consistent across all screens.
- **Feedback**: Provide feedback to the user when they interact with the interface.
- **Clarity**: Make sure the interface is clear and easy to understand.
- **Accessibility**: Make sure the interface is accessible to all users.
- **Responsiveness**: Make sure the interface is responsive and works well on all devices.

The question is then how do you apply these principles to AI Dungeon. The answer is to use the tools provided to you. As a creator you can control what story cards are presented to the player, what scripts are used, and what string interpolation is used and in what order. By using these tools you can create a better user experience for the player. A simple but important concept to understand is acceptance, if you fight the tools and player input you will lose. Instead, accept the tools and player input and work with them to create a better user experience.
You cannot please everyone all the time is an important idea to understand. Instead, focus on what you are good at and what you can control. Some people will never like what you make and that's ok. Focus on the people who do like what you make and make it better for them. Embrace the idea, and you will find you have a significant amount of control over the user experience.
A common issue is Story Cards, poor use of keys, and bad entries can lead to a poor user experience. The only part of the story card added to the context is the entry. If the entry is not clear or does not provide enough information the player might think the story card's title, keys, or value are added to the context. This can lead to confusion and a poor user experience. Instead, make sure the entry is clear and provides enough information for the AI. Imagine a story card for a character named John. The entry reads, "John is a carpenter who lives in the forest." However, the keys are "John,Carpenter,Forest" and the value is "John is a carpenter who lives in the forest." The player might think that the keys and value are added to the context and not the entry. This can lead to confusion and a poor user experience. Instead, make sure the entry is clear and provides enough information for the AI. Next look at the keys, the keys are used to reference the story card in the player's input. One of the keys is Forest, now every time the player mentions the forest the story card will be added to the context. This can lead to the story card being added to the context when it should not be or even the AI thinking that every forest has a John in it. Something else to keep in mind is the entry is concatenated to the context. This means that the entry is added to the context as is. If the entry is not clear or does not provide enough information the AI will not have enough information to generate a good response. A few tricks can be used. First, encapsulate the entry using some kind of demarcation like curly braces '{' or '}' This will make it clear where the entry starts and ends. Next, use string interpolation to provide more information. This can be done by using the keys in the entry. For example, "John is a carpenter who lives in the forest." can be changed to "John is a carpenter from the forest near the village of Weston." You can still get false positives. but this will help reduce them. Lastly, use the keys to reference other story cards. For people think about how they will be referenced in the story, people are referenced by name, title, and role. So for John, you might have keys like "John,The Carpenter,The Forest Dweller."
For usability and simplicity make sure both your questions are easy to understand and answer while not overloading the player with questions.
An example of poor UX is the overuse of string interpolation. When starting an adventure and being asked a dozen questions about your character it can be overwhelming. Apply the principle of simplicity and only ask the most important questions. This will make the user experience better for the player. Instead of asking the player to describe their character item by item encourage the player to describe their character in a single sentence. This will make the user experience better for the player, allow for greater player agency, and allow for a simpler and more streamlined character creation process. Instead of asking the player to describe their character's hair color, eye color, height, weight, and build ask the player to describe their character in a single sentence. This will make the user experience better for the player, allow for greater player agency, and allow for a simpler and more streamlined character creation process. Imagine this question is posed to the player "Describe your character, your desertion will be inserted at the course. I.E. `You are ${Describe yourself at the cursor. "You are |." I.E. "Eric, a man in his 30s and passionate about ecology"}.` This not only simplifies the character creation process but also allows for greater player agency and creativity. This is a simple example of how to apply the principles of UX to AI Dungeon.
A large part of consistency is out of your control as a creator, you will contend with player input, AI, AI settings, and more. However, you can control the consistency of the story cards, scripts, and string interpolation. By keeping these consistent you can create a better user experience for the player.
Feedback, without a current notification system on the comment system you can include links back to the Discord server, while this is not ideal it is a way to direct players to provide feedback.
For accessibility and responsiveness, you are largely at the mercy of the AI Dungeon developers.

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
    if(text.toLowerString().includes('you are made the emperor of rome')) {
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

## Plot and Story

### Plot and Story: Introduction

The plot and story are the backbone of any adventure. They provide the structure and context for the player's actions and interactions with the AI. The plot is the sequence of events that make up the story, while the story is the narrative that ties those events together. In AI Dungeon, the plot and story are generated by the AI based on the player's input and the context provided by the scenario. The player's input is used to guide the AI in generating the plot and story, while the context provides additional information that the AI can use to create a more coherent and engaging narrative. The plot and story are essential components of any adventure, and they play a crucial role in creating a compelling and immersive experience for the player.
It is important to note that the plot and story are generated by the AI, and the player's input is used to guide the AI in creating the narrative. The player's input can influence the direction of the plot and story, but the AI ultimately decides how the events unfold. The plot and story are dynamic and can change based on the player's actions and the context provided by the scenario. This allows for a high degree of replayability and variety in the adventures that can be created in AI Dungeon. This does not mean the scenario creator has no control over the plot and story. The scenario creator can use Story Cards, custom scripting to influence the AI and the plot.

## Closing

This document was created in large part using GitHub's Copilot AI. It is a demonstration of the capabilities of AI Dungeon and the potential for AI-generated content. The document covers the use of LLMs, custom scripting, and Story Cards in AI Dungeon. It explains how these features can be used to create custom scenarios and interactions in the game. The document serves as a guide for users and AI coding models to create custom content in AI Dungeon. It also serves as a form of configuration for AI LLMs with coding that the intended audience uses for interacting with AI LLMs to generate content for AI Dungeon. The document covers the following topics: LLMs in AI Dungeon, Custom Scripting in AI Dungeon, Story Cards in AI Dungeon, Considerations and Special Cases, and Examples. It provides a comprehensive overview of AI Dungeon and its features and is intended for AI Dungeon users and content creators who want to create custom scenarios and interactions in AI Dungeon using LLMs like chatGPT's GPTs and copilot. The document is a demonstration of the capabilities of AI Dungeon and the potential for AI-generated content. It is a testament to the power of AI and its ability to create engaging and interactive experiences for users.