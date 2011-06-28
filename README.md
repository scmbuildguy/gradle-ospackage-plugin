# Gradle RPM plugin

This plugin provides Gradle-based assembly of RPM packages, typically for Linux distributions
derived from RedHat.  It leverages [Redline](http://redline-rpm.org/) Java library.

## Usage

    apply plugin: 'rpm'

    // ...

    buildscript {
        repositories {
            mavenCentral()
        }

        dependencies {
            classpath 'com.trigonic:gradle-rpm-plugin:0.4'
        }
    }

    // ...

    task fooRpm(type: Rpm) {
        packageName = 'foo'
        version = '1.2.3'
        release = 1
        arch = I386
        os = LINUX

        installUtils = file('scripts/rpm/utils.sh')
        preInstall = file('scripts/rpm/preInstall.sh')
        postInstall = file('scripts/rpm/postInstall.sh')
        preUninstall = file('scripts/rpm/preUninstall.sh')
        postUninstall = file('scripts/rpm/postUninstall.sh')

        requires('bar', '2.2', GREATER | EQUAL)
        requires('baz', '1.0.1', LESS)
    
        into '/opt/foo'

        from(jar.outputs.files) {
            into 'lib'
        }
        from(configurations.runtime) {
            into 'lib'
        }
        from('lib') {
            into 'lib'
        }
        from('scripts') {
            into 'bin'
            exclude 'database'
            fileMode = 0550
        }
        from('src/main/resources') {
            directive = CONFIG
            into 'conf'
        }
    }

## Task

The RPM plugin is a copy task, similar to the Zip and Tar tasks.

