node {

    def appDir = '/var/www/nextjs-app'
    def nodeVersion = '18'
    def appName = 'nextjs-app'

    stage('Clean workspace') {
        echo 'Cleaning jenkins workspace'
        deleteDir()
    }


    stage('Clone Repo') {
        echo 'Cloning the repo'
        git(
            branch: 'main',
            url: 'https://github.com/rspacepointe-collab/react-app.git'
        )
    }

    stage('Deploy to EC2') {
    echo 'Deploy to EC2'
    sh """
        set -e

        APP_DIR=${appDir}

        sudo mkdir -p \$APP_DIR
        sudo chown -R jenkins:jenkins \$APP_DIR

        rsync -av --delete \
          --exclude='.git' \
          --exclude='node_modules' \
          ./ \$APP_DIR

        cd \$APP_DIR

        npm install
        npm run build

        sudo fuser -k 3000/tcp || true
        pm2 delete nextjs-app || true
        pm2 start npm --name "nextjs-app" -- start -- -H 0.0.0.0 -p 3000
        pm2 save
    """
}
}
