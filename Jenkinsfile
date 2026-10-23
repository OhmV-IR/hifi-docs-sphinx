// Builds the Sphinx HTML docs in docs/ on the Linux agent.
// The repository pins no Python dependencies, and conf.py uses APIs removed
// in Sphinx 3 and 4 (source_parsers, add_stylesheet), so Sphinx 2.4 and
// matching versions of its dependencies are pinned here instead.
pipeline {
    agent { label 'linux' }
    options {
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '20', artifactNumToKeepStr: '5'))
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Install dependencies') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install 'setuptools<70' 'sphinx==2.4.5' 'recommonmark==0.6.0' 'sphinx_rtd_theme==0.4.3' \
                        'docutils<0.18' 'jinja2<3.1' 'markupsafe<2.1' 'alabaster<0.7.14' \
                        'sphinxcontrib-applehelp<1.0.8' 'sphinxcontrib-devhelp<1.0.6' 'sphinxcontrib-htmlhelp<2.0.5' \
                        'sphinxcontrib-qthelp<1.0.7' 'sphinxcontrib-serializinghtml<1.1.10'
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    . .venv/bin/activate
                    cd docs
                    make html
                '''
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'docs/build/html/**', fingerprint: true, allowEmptyArchive: false
            }
        }
    }
}
