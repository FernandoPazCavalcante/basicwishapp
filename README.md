# Basic Wish App

A simple Terminal User Interface (TUI) application served over SSH that prompts users for their name and saves it to a log file.

## Overview

This project is a Go-based SSH server that runs a TUI application using the [Bubble Tea](https://github.com/charmbracelet/bubbletea) framework and [Wish](https://github.com/charmbracelet/wish) SSH server library. When users connect via SSH, they are presented with a simple interactive prompt asking for their name. Upon submission, the name is saved to `output.log`.

## How It Works

### Architecture

The application consists of several key components:

1. **SSH Server** (main.go:33-44): Creates an SSH server on `localhost:3000` using the Wish library
2. **Bubble Tea Middleware** (main.go:37): Integrates the TUI application with SSH sessions
3. **TUI Model** (main.go:73-117): Implements the interactive text input interface
4. **File Output** (main.go:102): Saves user input to `output.log`

### Flow

```
User connects via SSH
    ↓
SSH session established
    ↓
TUI displays "What is your name?" with text input
    ↓
User types their name
    ↓
User presses Enter
    ↓
Name is saved to output.log
    ↓
Session ends
```

### Key Features

- **Text Input**: Uses `charmbracelet/bubbles/textinput` for user input with placeholder "John Doe"
- **SSH Authentication**: Uses host key at `.ssh/id_ed25519` for server authentication
- **Graceful Shutdown**: Handles SIGINT and SIGTERM signals for clean shutdown
- **Logging**: Uses `charmbracelet/log` for server event logging

### Code Structure

- `main()` (main.go:32-63): Initializes and runs the SSH server
- `teaHandler()` (main.go:67-71): Creates the Bubble Tea model for each SSH session
- `initialModel()` (main.go:77-86): Sets up the initial state with text input
- `Update()` (main.go:92-111): Handles user input and key events
  - `Enter`: Saves input to `output.log` and quits
  - `Ctrl+C`: Quits without saving
- `View()` (main.go:113-117): Renders the UI

## Prerequisites

- Go 1.25.3 or higher
- SSH client (for testing)

## Installation

1. Clone the repository:
```bash
git clone https://github.com/FernandoPazCavalcante/basicwishapp.git
cd basicwishapp
```

2. Install dependencies:
```bash
go mod download
```

## Execution

### Start the Server

```bash
go run main.go
```

You should see output similar to:
```
INFO Starting SSH server host=localhost port=3000
```

### Stop the Server

Press `Ctrl+C` in the terminal running the server. You should see:
```
INFO Stopping SSH server
```

## Testing

### Method 1: Using SSH Client

1. Start the server:
```bash
go run main.go
```

2. In a separate terminal, connect via SSH:
```bash
ssh localhost -p 3000
```

3. You'll see the prompt:
```
What is your name?
John Doe
```

4. Type your name and press `Enter`

5. Check the output file:
```bash
cat output.log
```

### Method 2: Building and Running

1. Build the binary:
```bash
go build -o basicwishapp main.go
```

2. Run the binary:
```bash
./basicwishapp
```

3. Connect from another terminal:
```bash
ssh localhost -p 3000
```

### Expected Behavior

- The TUI should display "What is your name?" with a text input field
- You can type your name (placeholder shows "John Doe")
- Pressing `Enter` saves your name to `output.log` and closes the session
- Pressing `Ctrl+C` exits without saving
- Each time you submit a name, it overwrites the previous content in `output.log`

## Project Structure

```
basicwishapp/
├── main.go          # Main application code
├── go.mod           # Go module dependencies
├── go.sum           # Dependency checksums
├── output.log       # User input storage (created at runtime)
├── .ssh/
│   └── id_ed25519   # SSH host key (generated manually)
└── README.md        # This file
```

## Dependencies

- [charmbracelet/bubbletea](https://github.com/charmbracelet/bubbletea) - TUI framework
- [charmbracelet/bubbles](https://github.com/charmbracelet/bubbles) - TUI components
- [charmbracelet/wish](https://github.com/charmbracelet/wish) - SSH server
- [charmbracelet/ssh](https://github.com/charmbracelet/ssh) - SSH protocol implementation
- [charmbracelet/log](https://github.com/charmbracelet/log) - Logging library
