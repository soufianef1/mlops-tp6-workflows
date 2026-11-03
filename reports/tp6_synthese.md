# Orchestrateur ou integration continue ? (TP06)

| Critere                           | Prefect (orchestrateur)                                                                 | GitHub Actions (CI)                                                          |
|-----------------------------------|-----------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Ce qui declenche l'execution      | Une planification (cron lundi 6 h), un Quick run dans l'interface, ou l'API             | Un evenement Git : git push, ou le bouton Run workflow (workflow_dispatch)   |
| Ou s'execute le traitement        | Sur ma machine : processus serve (terminal 3) ou worker du work pool pool-tp            | Sur une machine Linux temporaire de GitHub (ubuntu-latest)                   |
| Qui surveille, et ou              | Interface Prefect (127.0.0.1:4200) : Runs, Deployments, Work Pools, journaux par tache | Onglet Actions de GitHub : runs, journaux par etape, statut sur le commit    |
| Ce qui se passe en cas d'echec    | Reessais sur rejouer_pipeline ; la tache valider echoue, le flow passe a Failed, notifier ne s'execute pas | L'etape "Verifier la regle de qualite" sort en code 1, le run est rouge, le commit est marque en rouge |
| Type de tache adapte              | Traitements recurrents de production : reentrainement, validation, notification         | Verification du code a chaque modification : pipeline rejoue, regle de qualite |
| Ce qui est versionne dans Git     | flow_mlops.py, planifier.py, prefect.yaml (le code du workflow, pas l'historique des runs) | .github/workflows/ci.yml, verifier_qualite.py, requirements-ci.txt          |

## Conclusion (5 lignes)

Prefect execute et surveille des traitements recurrents : il lance le reentrainement chaque lundi et s'arrete seul si le F1 passe sous le seuil.
GitHub Actions verifie le code a chaque git push : le run est devenu rouge avec max_depth: 2 (F1 = 0.25 < 0.82), puis vert apres git revert.
Les deux appliquent la meme regle de qualite (seuil_f1 de params.yaml), mais a des moments et a des endroits differents.
Prefect repond a la question "le traitement tourne-t-il bien ?", GitHub Actions a "cette modification du code est-elle acceptable ?".
Les deux sont complementaires : aucun ne remplace l'autre.