node('master') {
    try {
        // Set gem variables for Mobile CI.
        if (!(env.GEM_PATH && env.GEM_HOME)) {
            environment {
                GEM_PATH = sh 'ruby -rubygems -e "puts Gem.user_dir"'
                GEM_HOME = "${env.GEM_PATH}:${env.HOME}/.gems/bundler"
                PATH = "${env.GEM_HOME}/bin:${env.PATH}"
            }
        }

        stage('Clone') {
            git 'https://github.com/blue-systems/git-semaphore'
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
