---
title: git rebase --autosquash
---
git rebase --autosquash
=======================

*24 mai 2012*

J'utilise beaucoup la fonction `rebase` de Git en mode interactif, et je m'en
sers le plus souvent pour rajouter des choses que j'aurais pu oublier dans des
commits et pour réorganiser l'historique en général.

Une des choses que j'utilise pas mal c'est la fonction `squash`/`fixup` de `git
rebase`. Celle-ci permet de fusionner plusieurs commits en un seul.
La différence entre les deux modes se situe au niveau du message du commit : il
est retenu lors d'un `squash` et ignoré lors d'un `fixup`.

En parcourant le manuel de `git rebase` récemment j'ai fait la découverte de la
merveilleuse option `--autosquash` et de la directive de configuration associée
ainsi que des options `--squash` et `--fixup` de `git commit`.

Le principe est simple : plutôt que de manuellement réorganiser et marquer les
commits de `squash`/`fixup` avec l'éditeur que Git ouvre lors du `rebase`
interactifs, laisser Git gérer ça tout seul !

L'idée des options `--fixup` et `--squash` est qu'on leur précise un numéro de
commit et qu'elles vont permettre générer automatiquement le message de commit
depuis le titre du commit précisé, préfixé par `fixup!` ou `squash!`.

L'option `--autosquash` quant à elle va détecter automatiquement les commits
taggés et les placer à l'endroit adéquat dans l'éditeur.

Si c'était la seule chose qu'il fallait faire, il suffit de sauvegarder et
quitter pour appliquer le rebase, et même si Vim est extrêmement pratique à la
base pour éditer les fichiers de rebase, on gagne là un temps fou !

Un petit exemple pour la route :

``` sh
mon-projet (master:10826c0) $ git last
10826c0 (HEAD, master) First commit [madx]

mon-projet (master:10826c0) $ cat hello.rb
#!/usr/bin/env ruby
# encoding: utf-8
puts "Hello!"

mon-projet (master:10826c0) $ vim hello.rb
mon-projet (master*:10826c0) $ cat hello.rb
#!/usr/bin/env ruby
# encoding: utf-8
puts "Hello #{ARGV.first}!"

mon-projet (master*:10826c0) $ git ci -am 'Parameterize with ARGV'
[master 2c4e948] Parameterize with ARGV
 1 file changed, 1 insertion(+), 1 deletion(-)

mon-projet (master:2c4e948) $ vim hello.rb
mon-projet (master*:2c4e948) $ cat hello.rb
#!/usr/bin/env ruby
# encoding: utf-8
puts "Hello #{ARGV.first || "World"}!"

mon-projet (master*:2c4e948) $ git ci -a --fixup 2c4e948
[master 44b13af] fixup! Parameterize with ARGV
 1 file changed, 1 insertion(+), 1 deletion(-)
mon-projet (master:44b13af) $ git rebase -i
 1 pick 2c4e948 Parameterize with ARGV
 2 fixup 44b13af fixup! Parameterize with ARGV
```
