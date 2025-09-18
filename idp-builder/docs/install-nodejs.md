# How To Install The Latest Node.js

## 1. Install NVM

NVM (Node Version Manager) is a tool that allows you to manage multiple versions of Node.js on your machine. To install NVM, run the following command in your terminal:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

source ~/.bashrc
nvm --version
```

## 2. Install the Latest Node.js Version

Once NVM is installed, you can use it to install the latest version of Node.js. Run the following command:

```bash
nvm install node
nvm alias default node
```

This will install the latest version of Node.js and set it as the default version.

## 3. Verify the Installation

To verify that Node.js and npm (Node Package Manager) are installed correctly, run the following commands:

```bash
node -v
npm -v
```

