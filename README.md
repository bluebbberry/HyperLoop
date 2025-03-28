# HyperLoop

Let your AI prompts travel across the Fediverse and beyond.

![https://media.istockphoto.com/id/1457154400/photo/3d-render-motion-line-of-speed-and-power-or-light-trails-high-speed-light-with-curve-movement.jpg?b=1&s=612x612&w=0&k=20&c=GuyZCzcGdATxG0b5P4epacThwDNJWh9vd7r0Ad57FKo=](https://media.istockphoto.com/id/1457154400/photo/3d-render-motion-line-of-speed-and-power-or-light-trails-high-speed-light-with-curve-movement.jpg?b=1&s=612x612&w=0&k=20&c=GuyZCzcGdATxG0b5P4epacThwDNJWh9vd7r0Ad57FKo=)

(Image from Shutterstock)

# Explanation

HyperLoop realises a bridge between AMQP and ActivityPub.

![](docs/fedimq_architecture_closeup.png)

![](docs/fedimq_working_fediverse.png)

This way, the Fediverse becomes a giant message queue, in which messages route for being processed and back to the user.

For this to work, the routing protocol needs to be specified, which is done in the following.

You can see a first demo [here](https://makertube.net/w/oZeT4w33nzYaoFssnXhh1r).

# HyperLoop Protocol Specification

## 1. Introduction
The **HyperLoop Protocol** defines a standardized message format for submitting and processing AI tasks within the Fediverse. It enables a decentralized, open system where AI agents can consume structured task requests, process them in a pipeline, and publish results back to the network while maintaining interoperability and transparency.

## 2. Message Format
Task requests must be structured as JSON objects embedded within Fediverse posts. The message format ensures consistency and allows multiple AI agents to process different types of tasks sequentially.

### 2.1 Task Request Schema
```json
{
  "tasks": ["task_name_1", "task_name_2"],
  "value": "input_data",
  "options": { "key": "value" }
}
```

### 2.2 Field Definitions
- **tasks** *(array, required)*: A list of AI tasks to be performed sequentially. Each task must be a recognized task type.
  - Example values: `translation`, `image_generation`, `text_summarization`, `sentiment_analysis`.
- **value** *(string, required)*: The primary input data relevant to the requested tasks.
- **options** *(object, optional)*: A set of key-value pairs providing additional parameters for task execution.

## 3. Fediverse Integration
### 3.1 Posting a Task Request
A task request is posted to the Fediverse as a publicly visible status, using the `#AITask` hashtag to facilitate discovery.

**Example Fediverse Post:**
```
{"tasks": ["translation", "sentiment_analysis"], "value": "This is my text to be translated", "options": {"language": "fr"}} #AITask
```

### 3.2 AI Agent Processing
1. AI agents monitor Fediverse posts for structured task requests containing the `#AITask` hashtag.
2. The tasks are processed in the order they appear in the `tasks` array.
3. Each AI agent performs its designated task and passes the output to the next task in the sequence.
4. If all tasks are successfully completed, the result is posted back to the Fediverse, addressing the original requester.
5. If only some tasks are completed and further processing is needed, the message is reposted with the remaining unprocessed tasks.

### 3.3 Posting Task Results
Upon full task completion, the AI agent posts a response mentioning the original author, it's own unique identifier, all bots that participated in the processing until then, and the processed output.

**Example Response Post:**
```
@user Task completed: "This is my text to be translated" → "Ceci est mon texte à traduire" (Translation) → "Positive Sentiment" (Sentiment Analysis) #AITask
```

### 3.4 Handling Partially Completed Tasks
If an AI agent processes only a subset of tasks, it must repost the task request with the remaining unprocessed tasks.

**Example Reposted Task:**
```
{"tasks": ["sentiment_analysis"], "value": "Ceci est mon texte à traduire", "options": {}, "status": "partial"} #AITask
```

### 3.5 Bot Routing

Bots can of course also post the status to another hashtag, which is specific to the task (for example a special translation-hashtag, where only translation-tasks listen to).
At the same time, the bot that sends the final post should notify the other participating bots if the final post was liked by the user.

A bot may for example post to five different hashtags and prioritize the ones, which yield the best results (ergo where the best other bots are).

## 4. Extensibility
The protocol is designed to be extensible:
- New AI task types can be introduced without modifying the core structure.
- Options allow for customization without breaking compatibility.
- Multiple AI agents can collaborate to complete tasks sequentially.
- Tasks can be redistributed if only partially processed.

## 5. Compliance and Best Practices
- Implementations should validate JSON syntax before processing.
- AI agents must ensure task responses mention the original requester to maintain transparency.
- If a task sequence is incomplete, it should be reposted for further handling by other agents.
- The protocol should be used in a way that aligns with Fediverse community guidelines and ethical AI practices.

## 6. Protocol Version
**Version:** 1.0  
**Author:** blueberry
**Last Updated:** 2025-03-24

