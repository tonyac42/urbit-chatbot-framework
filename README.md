# Urbit Chatbot Framework

This Rust crate allows anyone to create an Urbit Chatbot with only a few lines of code. The Urbit Chatbot Framework takes care of all the complexities of connecting to a ship, subscribing to a chat, parsing messages, and sending messages automatically for you. All you have to do is simply write the message handling/response logic and everything else will just work.

## Basic Design

At its core, the framework revolves around the `Chatbot` struct. It is defined as:

```rust
/// This struct represents a chat bot that is connected to a given `ship`,
/// is watching/posting to a specific `chat_ship`/`chat_name`
/// and is using the function `respond_to_message` to process any messages
/// which are posted in said chat.
pub struct Chatbot {
    /// `respond_to_message` is a function defined by the user of this framework.
    /// This function receives any messages that get posted to the connected chat,
    /// and if the function returns `Some(message)`, then `message` is posted to the
    /// chat as a response. If it returns `None`, then no message is posted.
    respond_to_message: fn(AuthoredMessage) -> Option<Message>,
    ship: ShipInterface,
    chat_ship: String,
    chat_name: String,
}
```

To create a chatbot, one simply must instantiate a `Chatbot` struct with:

- A `ShipInterface` to connect to to interact with the Urbit network
- The `chat_ship` that the chat is running on
- The `chat_name`
- A function (optionally named respond_to_message) which takes an `AuthoredMessage` as input, and returns an `Option<Message>` as output

## Creating A Chatbot

A `Chatbot` is most easily created using the `new_with_local_config` method:

```rust
/// Create a new `Chatbot` with a `ShipInterface` derived automatically
/// from a local config file. If the config file does not exist, the
/// `Chatbot` will create the config file, exit, and prompt the user to
/// fille it out.
pub fn new_with_local_config(
    respond_to_message: fn(AuthoredMessage) -> Option<Message>,
    chat_ship: &str,
    chat_name: &str,
) -> Self {
```

This automatically deals with creating a local ship config file for the user to edit, and then reading it.

In order to use this method, we clearly need to define a `respond_to_message` function which we can supply as an argument. Here is an example of one of the simplest possible implementations:

```rust
fn respond_to_message(authored_message: AuthoredMessage) -> Option<Message> {
    // Any time a message is posted in the chat, respond in chat with a static message.
    Some(Message::new().add_text("Calm Computing ~"))
}
```

Do note, that if this method returns `None`, then the `Chatbot` will not reply to the message, and simply continue forward processing the next one.

With this function defined, you can now easily call `Chatbot::new_with_local_config()` and acquire an instantiated `Chatbot`.

## Running The Chatbot

Once the `Chatbot` struct is defined, all one needs to do is:

```
chat_bot.run();
```

This will automatically perform all messaging, parsing, and interfacing with the connected Urbit ship without any further code required.

And just like that you have an Urbit chatbot ready to go.

# Example Projects

The projects linked below are example projects which create Urbit Chatbots. They link directly to the `main.rs` so you can immediately see the implementation of said chatbot.

You can easily run any of these examples by:

1. Cloning this repo.
2. Moving into the example project folder in your terminal.
3. Editing `main.rs` with the `chat_ship` and `chat_name` which you wish to run the bot in.
4. Running `cargo run` the first time to create the ship config file.
5. Editing the `ship_config.yaml` with your ship information.
6. Running `cargo run` to now run the chatbot with the ship config info provided.

### Simple Chatbot

[The Simple Chatbot](examples/simple-chatbot/src/main.rs) is the simplest chatbot possible which replies to all messages in a chat with a static message. Approximately 4 lines of real code.

The point of this project is to show off the bare minimum requirements for setting up a chatbot, and just how easy it is.

### Anti Comet Chatbot

[The Anti Comet Chatbot](examples/anti-comet-chatbot/src/main.rs)

### Crypto Prices Chatbot

[The Crypto Prices Chatbot](examples/crypto-prices-chatbot/src/main.rs)
