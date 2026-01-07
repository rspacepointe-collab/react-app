node {

    def APP_DIR = '/var/www/nextjs-app'
    def APP_NAME = 'nextjs-app'

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins workspace'
        deleteDir()
    }

    stage('Clone Repository') {
        echo 'Cloning Git repository'
        git branch: 'main',
            url: 'https://github.com/rspacepointe-collab/react-app.git'
    }

    stage('Build & Deploy') {
        echo 'Deploying Next.js App'
        sh """
            set -e

            echo "Creating app directory"
            sudo mkdir -p ${APP_DIR}
            sudo chown -R jenkins:jenkins ${APP_DIR}

            echo "Syncing source code"
            rsync -av --delete \
              --exclude='.git' \
              --exclude='node_modules' \
              ./ ${APP_DIR}

            cd ${APP_DIR}

            echo "Installing dependencies"
            npm install

            echo "Building Next.js app"
            npm run build

            echo "Installing PM2 if missing"
            if ! command -v pm2 >/dev/null 2>&1; then
              sudo npm install -g pm2
            fi

            echo "Stopping old app if running"
            sudo pm2 delete ${APP_NAME} || true

            echo "Starting app with PM2 (ubuntu user)"
            sudo pm2 start npm \
              --name ${APP_NAME} \
              --cwd ${APP_DIR} \
              -- start -- -H 0.0.0.0 -p 3000

            echo "Saving PM2 process list"
            sudo pm2 save

            echo "Enabling PM2 startup on reboot"
            sudo pm2 startup systemd -u ubuntu --hp /home/ubuntu
        """
    }
}
