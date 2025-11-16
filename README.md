# IPMI-FanPilot

A simple and effective IPMI fan controller for servers. Simply enter your server's IP, username, and password, then tweak the fan speed or set it to automatic mode. Save your credentials securely and adjust settings anytime through a clean web interface. It's a simple and handy tool to keep your servers cool and quiet.

---

## Requirements

This application requires the following components to run properly:

- **Node.js 18.0 or higher** (for direct installation)
- **npm** (Node Package Manager)
- **ipmitool** installed and accessible
  - Windows: Add to PATH or use PowerShell
  - Linux: `apt-get install ipmitool`
  - Docker: Pre-installed in image
- A server with IPMI interface enabled (Dell, HP, Lenovo, Supermicro, IBM)

---

## Installation

### Option 1: Node.js Direct Installation

#### Step 1: Install Node.js

Download and install Node.js from https://nodejs.org/ (LTS version recommended). This will automatically include npm.

#### Step 2: Install ipmitool

**Windows:**
Download ipmitool for Windows and add it to your system PATH so it can be executed from any directory. Ensure the command works by opening Command Prompt and running:

    ipmitool -V

**Linux:**
Install via package manager:

    sudo apt-get install ipmitool

#### Step 3: Clone or Download the Repository

Navigate to where you want to install the application and clone the repository or download the files.

    git clone https://github.com/dev-luigi/IPMI-FanPilot.git
    cd IPMI-FanPilot

#### Step 4: Install Project Dependencies

Install all required Node.js packages listed in package.json:

    npm install

This will create a node_modules folder containing Express.js and all other dependencies.

---

### Option 2: Docker (Recommended)

IPMI-FanPilot is available as a Docker image for easy deployment!

#### Method A: Build Locally with Dockerfile

```bash
# Clone the repository
git clone https://github.com/dev-luigi/IPMI-FanPilot.git
cd IPMI-FanPilot

# Build the image
docker build -t ipmi-fanpilot:latest .

# Run the container
docker run -p 3000:3000 ipmi-fanpilot:latest
```

Access at: http://localhost:3000

#### Method B: Pull Pre-built Image from Docker Hub

```bash
# Pull the official image
docker pull devluigi06/ipmi-fanpilot:latest

# Run the container
docker run -p 3000:3000 devluigi06/ipmi-fanpilot:latest
```

Access at: http://localhost:3000

**Docker Hub Repository:** https://hub.docker.com/r/devluigi06/ipmi-fanpilot

#### Method C: Docker Compose

Create a `docker-compose.yml` file in your project directory:

```yaml
version: '3.8'
services:
  ipmi-fanpilot:
    image: devluigi06/ipmi-fanpilot:latest
    ports:
      - "3000:3000"
    volumes:
      - ipmi-fanpilot-data:/app
    restart: unless-stopped

volumes:
  ipmi-fanpilot-data:
```

Then run:

```bash
docker-compose up -d
```

---

## Running the Application

### Direct Installation (Node.js)

#### Using the Start Script (Windows)

Simply double-click the start.bat file. The script will automatically:
- Request administrator privileges
- Install dependencies if needed
- Start the Node.js server
- Display any errors if they occur

#### Using Command Prompt or PowerShell

Navigate to the application folder and run:

    npm start

or

    node server.js

The server will start on port 3000. Open your browser and go to:

    http://localhost:3000

### Docker Installation

The application automatically starts when the container runs. Access at http://localhost:3000

---

## First Time Setup

On your first launch:

1. Open http://localhost:3000 in your browser
2. Enter your server IPMI credentials:
   - IP iDRAC: The IP address of your server management interface
   - Username: IPMI username (usually 'root')
   - Password: IPMI password
3. Click the green "Save" button
4. The credentials are automatically encrypted and saved to credentials.json

**Important:** The credentials.json file is encrypted with AES-256 encryption and is unreadable without the application's decryption key. You do not need to manually create this file.

---

## Usage

1. Open the application in your web browser at http://localhost:3000
2. On first run, enter your server IPMI credentials and click "Save"
3. On subsequent runs, your credentials will load automatically from the encrypted file
4. Use the dial to adjust fan speed from 0-100%
5. Click "Confirm" to apply manual speed settings
6. Click "Auto Mode" to let the server control fans automatically
7. View command history at the bottom to verify execution
8. Click the red "Clear" button to delete saved credentials (you'll need to re-enter them next time)

---

## Project Structure

    IPMI-FanPilot/
    ├── server.js                 Main Node.js application
    ├── package.json              Project dependencies
    ├── package-lock.json         Dependency lock file
    ├── Dockerfile                Docker configuration
    ├── docker-compose.yml        Docker Compose setup
    ├── README.md                 This file
    ├── SECURITY.md               Security guidelines
    ├── LICENSE                   MIT license
    ├── .gitignore                Git ignore configuration
    ├── .dockerignore             Docker ignore configuration
    │
    ├── public/
    │   └── index.html            Web interface dashboard
    │
    └── credentials.json          Encrypted credentials (auto-generated)
                                   *This file is created automatically on first save*
                                   *Not included in repository for security*

---

## Security Features

- **Encrypted Storage:** Credentials are encrypted using AES-256-CBC encryption
- **No Plain Text:** No credentials stored in plain text on disk
- **Unique IV:** Each encryption uses a unique Initialization Vector
- **Input Validation:** IPMI commands protected against injection attacks
- **Secure Execution:** Commands executed with validation
- **Auto-Generated File:** credentials.json is created automatically, no manual setup needed
- **Security Patched:** All CVEs resolved in Docker image
- **Non-root Container:** Docker runs as non-root user for security

For detailed security information, see `SECURITY.md`

---

## Available Endpoints

The application provides the following API endpoints:

- `POST /api/execute` - Execute IPMI fan commands (manual or auto mode)
- `POST /api/credentials/save` - Save connection credentials (encrypted)
- `GET /api/credentials/load` - Load saved credentials (decrypted)
- `DELETE /api/credentials/clear` - Delete saved credentials
- `GET /api/health` - Server health check

---

## Troubleshooting

### Application won't start

- Ensure Node.js is installed: `node --version`
- Verify npm is installed: `npm --version`
- Check that ipmitool is in PATH: `ipmitool -V`
- Run start.bat as administrator
- Check Windows Firewall settings
- Verify port 3000 is not in use: `netstat -ano | findstr :3000`

### Credentials won't save

- Ensure the application has write permissions in the folder
- Verify all three fields (IP, username, password) are filled
- Check browser console for error messages (F12)
- Verify disk space is available

### IPMI commands fail

- Verify server IPMI is enabled and network accessible
- Test connectivity: `ping 192.168.x.x` (replace with your IPMI IP)
- Verify credentials are correct
- Check server logs for IPMI errors
- Ensure ipmitool supports your server model

### Port 3000 already in use

Find and kill the process using port 3000:

    netstat -ano | findstr :3000
    taskkill /PID <PID> /F

Or change the PORT variable in server.js or use a different port with Docker:

    docker run -p 3001:3000 devluigi06/ipmi-fanpilot:latest

---

## Supported Servers

This application works with any server that supports IPMI 2.0 including:

- **Dell PowerEdge:** R620, R640, R650, R720, R730, R750 (12G-13G series)
- **Supermicro:** All models with IPMI BMC
- **HP ProLiant:** All models with ILO
- **Lenovo ThinkSystem:** All models with XCC
- **IBM System X:** All models with IMM

**Note:** Some newer Dell 14G/15G servers (R740XD, R750) may have IPMI fan control blocked by firmware as a security restriction from Dell.

Ensure IPMI is enabled and accessible on your network.

---

## Features

- 🌐 Web-based dashboard with intuitive fan speed dial
- 🎚️ Manual fan speed control (0-100%)
- ⚙️ Automatic mode (server manages fan speed)
- ⏱️ Preset speed buttons (20%, 30%, 50%, 70%, 100%)
- 🔒 Secure credential storage (AES-256 encryption)
- 💾 Save/load/clear credentials automatically
- 📋 Command history log
- 📊 Real-time status messages
- 📱 Responsive design for desktop and tablet
- ⚠️ Error handling with clear messages
- 🐳 Docker support with pre-built image
- 🔄 Cross-platform support (Windows, Linux, Docker)
- ✅ All security vulnerabilities patched

---

## Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.

To contribute:
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

---

## Author

Luigi Tanzillo  
GitHub: https://github.com/dev-luigi  
Docker Hub: https://hub.docker.com/r/devluigi06/ipmi-fanpilot  

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.

---

## Disclaimer

This tool is provided as-is for managing IPMI-enabled servers. Use at your own risk. Always test in a non-production environment first. Improper fan control can damage hardware or void warranties. The author is not responsible for any damage caused by misuse of this application.

---

## Support

For issues, questions, or suggestions:
- Open an issue on GitHub: https://github.com/dev-luigi/IPMI-FanPilot/issues
- Check Docker Hub: https://hub.docker.com/r/devluigi06/ipmi-fanpilot
- Review documentation and SECURITY.md
- Check server logs for errors

---

**Last Updated:** November 2025  
**Version:** 1.0.0