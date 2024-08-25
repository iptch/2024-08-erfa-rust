# Exercises

## Setting up your environment

In order to install all the required tools to get started, run the following in this directory:

```bash
devbox shell
```

Once you are in the shell, you will have all the tools you need to get started with Rust.

## Initialising Rustling Exercises

> [!NOTE]
> You need to be within the Devbox shell to execute the following commands.

Run the following commands:

```bash
cargo install rustlings
export PATH="${PATH}:~/.cargo/bin"
rustlings init
```

This will download all the rustlings exercises into the directory and make them ready to use for
you.

After the rustlings exercises have downloaded, you can start them by navigating into the
`rustlings` directory and entering the command `rustlings`. 
From there on, the exercise interface will guide you.
