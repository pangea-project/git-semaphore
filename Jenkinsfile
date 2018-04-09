node('master') {
    try {
        // Set gem variables for Mobile CI.
        if (!(env.GEM_PATH || env.GEM_HOME)) {
            echo 'Setting GEM paths to HOME for Mobile CI'
            env.GEM_PATH = sh(script: 'ruby -rubygems -e "puts Gem.user_dir"',
                              returnStdout: true).trim()
            env.GEM_HOME = "${env.GEM_PATH}:${env.HOME}/.gems/bundler"
            env.PATH = "${env.GEM_PATH}/bin:${env.PATH}"
            sh 'env'
        }

        stage('Clone') {
            checkout scm
        }
        stage('Bundle') {
            sh '[ -f Gemfile.lock ] || bundle install'
            sh 'bundle update'
        }
        stage('Test') {
            sh 'rake test'
            junit 'test/reports/*.xml'
        }
        stage('Checkout') {
            sh 'git checkout-index -a -f --prefix=$HOME/git-semaphore/'
        }
    } catch (e) {
        stage('Notify') {
            sh 'gem install ci-notify'
            sh 'env'
            sh 'IRC_CHANNEL=#pangea-monitoring ci-notify FAILURE'
        }
        throw e
    }
}
