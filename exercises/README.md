# Exercises

We use Rustlings to gain a quick and very interactive overview of Rust. 
Rustlings is a project that provides exercises on all the different topics that make up Rust.
It also provides a binary that you can run with `rustlings`, which will guide you through the exercises and does hot reloads (recompiling your exercise) upon changes on the files.

## Setting up your environment

In order to install all the required tools to get started, run the following in this directory:

```bash
devbox shell
```

Once you are in the shell, you will have all the tools you need to get started with Rust.

### For New ARM Mac Users

Do **not** use devbox but install the rust toolchain directly on your system from https://www.rust-lang.org/tools/install. 
The rest of the instructions is the same as for the devbox setup.

## Initialising Rustling Exercises

> [!NOTE]
> If you did not install Rust through rustup, you need to be within the Devbox shell to execute the following commands.

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
