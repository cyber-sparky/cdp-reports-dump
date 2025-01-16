
## Generic workflows 

Involves like general scanning purposes like dot file exposure, misconfigs and zero days 

```yml
workflows:
  - template: files/git-config.yaml
  - template: files/svn-config.yaml
  - template: files/env-file.yaml
  - template: files/backup-files.yaml
  - tags: xss,ssrf,cve,lfi
```

## Conditional workflows

Tech stack based scanning we can say like let's say our application utilises wordpress and we can scan with wordpress related templates 

Here we have mentioned with examples on vbulletin and jboss 


```yml
workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: vbulletin
        subtemplates:
          - template: exploits/vbulletin-exp1.yaml
          - template: exploits/vbulletin-exp2.yaml
      - name: jboss
        subtemplates:
          - template: exploits/jboss-exp1.yaml
          - template: exploits/jboss-exp2.yaml
```

## Custom workflow example


```yml
id: my-custom-workflow

info:
  name: Django workflow
  author: pdso

workflows:
  - template: http/technologies/tech-detect.yaml
    matchers:
      - name: nginx
        subtemplates:
          - template: http/exposed-panels/django-admin-panel.yaml
          - template: http/misconfiguration/django-debug-detect.yaml
          - template: http/technologies/default-django-page.yaml
          - template: http/exposures/files/django-secret-key.yaml
          - template: http/exposures/configs/django-variables-exposed.yaml
          - template: http/exposures/logs/django-debug-exposure.yaml
          - template: file/logs/django-framework-exceptions.yaml
```