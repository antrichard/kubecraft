Pourquoi `ethernet-1/1` est `down` ?

Si tu regardes l'état opérationnel de ton interface (`Oper State`), elle affiche **`down`**, alors qu'au tout début de ton lab, elle était `up`.

**La raison :** Tu as relancé le conteneur directement via **`docker start`** au lieu d'utiliser Containerlab.

- Containerlab utilise des liens virtuels (veth pairs) créés à la volée dans l'espace réseau Linux pour relier les conteneurs entre eux.
- Lorsque `srl2` s'est arrêté ou a été recréé, ces liens virtuels gérés par Containerlab ont été détruits.
- En faisant un simple `docker start`, Docker rallume le conteneur avec sa seule interface réseau par défaut (`mgmt0`), mais il ne sait pas recréer les câbles virtuels (`ethernet-1/1`) qui le reliaient à `srl1`.

---

🛠️ Comment réparer les câbles réseau sans tout réinitialiser ?

Pour réinjecter les liens réseau manquants sans détruire ton lab, Containerlab possède une commande magique dédiée à cela. Exécute simplement depuis ton dossier :

```bash
clab deploy -t topology/lab.clab.yml --graph
# Ou si tu es dans le dossier de la topologie :
clab deploy -t lab.clab.yml
```

Containerlab va détecter que les conteneurs tournent déjà, mais que les liens réseau (`links`) sont manquants, et il va **rebrancher instantanément** `ethernet-1/1` entre `srl1` et `srl2`. Ton interface repassera immédiatement à `up`.
