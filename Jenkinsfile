node('master') {
    try {
        stage('Clone') {
            git 'https://github.com/blue-systems/git-semaphore'
        }
        stage('Bundle') {
            sh '[ -f Gemfile.lock ] || bundle install'
            sh 'bundle update'
        }
        stage('Test') {
            sh 'rake test'
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
