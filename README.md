# HomelabTestProject-v.1

### Project Description

Repurposing my old laptop to a test server running multiple services (ie. JellyFin, Pihole, NextCloud, etc.) using an Ubuntu Server.

### Table of Contents

- [HomelabTestProject-v.1](#homelabtestproject-v.1)
- [Project Description](#project-description)
- [Table of Contents](#table-of-contents)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

### Features

- Offline media server (Jellyfin)
- Self-hosted cloud storage accessible to the internet (NextCloud)
- Web ad-blocker, site blocker (DNS Filtering) (Pi-hole)

### Technologies Used

![dell7447](https://github.com/KevJimenez/HomelabTestProject-v.1/blob/main/photo_2023-10-11_11-27-36.jpg?raw=true)

 **Server Specs:**
 - Laptop Model: Dell Inspiron 14 7447
   - Processor: `Intel i7-4710HQ (4C | 8T)`
   - Memory: `8GB DDR3L 1600mhz`
   - Graphics: `Intel HD 4600 (Integrated), Nvidia GTX 850M (Dedicated)`
   - Storage: `128GB SSD (OS Drive), 140GB HDD (Storage Drive`
>         
**Others:**
- Host PC/Laptop
- Cat 5e LAN Cables (For 1 gigabit transfer speeds) 
- Huawei Echolife EG8145V5 (Router)
- Ubuntu Server 22.04.3 LTS
- Docker
- Htop
- OpenSSH
- Jellyfin
- NextCloud
- Pi-hole
### Getting Started

#### Prerequisites

**Docker Supported OS (for Ubuntu):**
- Ubuntu Lunar 23.04
- Ubuntu Kinetic 22.10
- Ubuntu Jammy 22.04 (LTS)
- Ubuntu Focal 20.04 (LTS)

#### Installation
**Htop Setup**
1. Run apt update and apt upgrade in the CLI

   ```bash
   sudo apt update && sudo apt upgrade
   ```
2. Install htop using the apt command

   ```bash
   sudo apt install htop
   ```
   
**Docker Setup:**
1. Uninstall old docker packages (It is highly recommended to run this even though you're on a fresh OS)
   
   ```bash
   for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
   ```
2. Install the Docker Repository

   ```bash
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    
    # Add the repository to Apt sources:
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```
3. Install Docker Package

   ```bash
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
4. Verify that Docker is installed

    ```bash
    sudo docker run hello-world
    ```

   

### Usage

Explain how to use the project once it's set up. Provide examples, screenshots, or use cases to demonstrate its functionality.

### Configuration

If your project has configuration options, document how users can customize and configure the settings.

### Contributing

Explain how others can contribute to your project if it's open-source. Include guidelines for pull requests, issue reporting, and code style.

### License

Specify the project's license (e.g., MIT License, GNU GPL) and provide a link to the full license text.

### Acknowledgments

Give credit to any individuals, libraries, or resources that were helpful in the development of your project.

### Additional Sections

Depending on the nature of your HomeLab project, you may want to include additional sections such as:

- Troubleshooting: Provide solutions to common issues users might encounter.
- FAQ: Answer frequently asked questions.
- Roadmap: Outline future development plans and features.
- Changelog: Document version history and updates.

### Example README Template

Here's a simplified example of what a HomeLab project README might look like in Markdown:

```markdown
# HomeLab Project: Smart Home Automation

## Project Description

A smart home automation system to control lights, appliances, and security cameras.

## Table of Contents

- [Project Description](#project-description)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

## Features

- Voice-controlled lighting
- Remote monitoring of security cameras
- Integration with smart home devices

## Technologies Used

- Raspberry Pi
- Home Assistant
- Zigbee
- MQTT

## Getting Started

### Prerequisites

- Raspberry Pi 4 or higher
- Home Assistant installed and configured
- Zigbee dongle

### Installation

1. Clone this repository.
2. Install the required dependencies with `pip install -r requirements.txt`.
3. Configure your Home Assistant server.

## Usage

1. Start the Home Assistant server.
2. Connect your Zigbee dongle.
3. Configure devices in the Home Assistant dashboard.

## Configuration

You can customize the system by editing the `config.yaml` file.

## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Special thanks to the Home Assistant community for their support and contributions.
```

Customize the sections and content to fit the specifics of your HomeLab project. Including clear and comprehensive documentation in your README will help others understand, use, and contribute to your project more effectively.
