# Build project using Jenkins

This recipe is about how to setup Jenkins to automatically build project and deploy it to GS bucket.

## Přidání Jenkinsfile do projektu
TBD - kde vzít Jenkinsfile?

* Změnit `projectName`
* PipelineReact - název funkce v groovy skriptu, která obsluhuje pipelinu
* `slackChannel` - Nastavit název slack kanálu pro konkrétní projekt, např. #ci-petshare
* Přidat větve pro buildění i jejich adresy pro deploy

## Založení nového jobu

1. Otevřít https://jenkinsaurora.ack.ee/
2. Vlevo nahoře Nové
3. První políčko, vyplnit název projektu
4. Vytvořit job
  * Nový Create multipipeline branch
  * Vytvořením na základě jiného jobu (např. abibuch/abibuch-editor-frontend)
5. Potvrdit, dostaneme se na stránku konfigurace jobu, změníme pole Project repository
6. Nastavíme branche, které se mají skenovat, jejich vyjmenováním (mezi branchemi je mezera) , např. master development stage v poli Behaviours -> Discover branches -> include
7. Rozhodnout se zda nechat Supress automatic SCM triggering, pro větev master - jestli push do master větve vyvolá build.
8. Uložit konfiguraci
9. Nastavit webhook
   1. V Jenkinsi je adressa pro wehook neznámo kde, takže nejlepší je doplnit tuto šablonu http://jenkinsaurora.ack.ee/git/notifyCommit?url=git@gitlab.ack.ee:JMENO_GROUPY/JMENO-REPA.git, tedy např. http://jenkinsaurora.ack.ee/git/notifyCommit?url=git@gitlab.ack.ee:Web/abibuch-editor.git
   2. V gitlabu u repozitáře jít do Settings -> Integration a do prvního pole URL vložit adresu, kliknout Add webhook (zkontrolovat že je zaškrtnuto Enable SSL verification)




