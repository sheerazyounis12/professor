#!/bin/bash

# Rubber Ducky script to create a backdoor to shutdown the system

# Function to check if xdotool is installed
check_xdotool() {
    if ! command -v xdotool &> /dev/null; then
        echo "Error: xdotool is not installed. Please install it before running this script."
        exit 1
    fi
}

# Function to check if the system is using GNOME
check_gnome() {
    if [ "$XDG_CURRENT_DESKTOP" != "GNOME" ]; then
        echo "Error: This script is designed for GNOME desktop environment. Your environment is not supported."
        exit 1
    fi
}

# Function to prompt the user for confirmation
prompt_confirmation() {
    read -p "Are you sure you want to shut down the system? (y/n): " confirm
    if [ "$confirm" != "y" ] && [ "$confirm" != "Y" ]; then
        echo "Shutdown canceled."
        exit 0
    fi
}

# Function to execute the shutdown command
execute_shutdown() {
    # Delay for 10 seconds
    sleep 10

    # Open a new terminal window
    xdotool key ctrl+alt+t

    # Type the command to shutdown the system
    xdotool type "sudo shutdown now"

    # Press Enter to execute the command
    xdotool key Return

    # Perform additional actions (e.g., lock the screen)
    gnome-screensaver-command -l

    # Check if the shutdown command was successful
    if [ $? -eq 0 ]; then
        echo "System has been shut down successfully."
    else
        echo "Error: Failed to shut down the system."
        exit 1
    fi
}

# Function to handle script execution
main() {
    # Check if the script is being run with sudo privileges
    if [ "$EUID" -ne 0 ]; then
        echo "Error: This script must be run with sudo privileges."
        exit 1
    fi

    # Check dependencies
    check_xdotool
    check_gnome

    # Prompt the user for confirmation
    prompt_confirmation

    # Execute the shutdown command
    execute_shutdown
}

# Call the main function
main
