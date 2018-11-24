
#### A lot of problems are solved by making sure you're using the latest version of NodeJS, npm and the CLI itself.

#### Updating NodeJS:

Go to nodejs.org and download the latest version - uninstall (all) installed versions on your machine first.

#### Updating npm:

Run:

```sh
[sudo] npm install -g npm  (sudo  is only required on Mac/ Linux)
```

#### Updating the CLI

```sh
[sudo] npm uninstall -g angular-cli @angular/cli 
npm cache clean 
[sudo] npm install -g @angular/cli
```

