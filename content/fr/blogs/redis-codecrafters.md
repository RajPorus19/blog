---
title: "Comment j'ai reconstruit Redis from scratch (et ce que ça m'a appris)"
description: "Mon expérience avec le challenge Codecrafters Redis en Go"
date: 2026-05-25
image: "/images/project-placeholder.svg"
tags:
  - "go"
  - "redis"
  - "apprentissage"
  - "backend"
mathjax: false
---

## Pourquoi reconstruire Redis ?

Redis est partout. Cache, sessions, files d'attente, rate limiting... Mais tant qu'on n'a pas **écrit** un serveur Redis, on ne comprend pas vraiment comment ça marche.

Je me suis lancé le challenge **Codecrafters** : reconstruire Redis from scratch en Go. Voici ce que j'ai appris en chemin.

## Le protocole RESP

La première étape était de parser le protocole **RESP** (Redis Serialization Protocol). C'est un protocole texte simple mais élégant :

```
*3\r\n$3\r\nSET\r\n$5\r\nmykey\r\n$5\r\nhello\r\n
```

Chaque commande est représentée par :
- Un **type** (string, error, integer, bulk string, array)
- Une **taille** pour les bulk strings
- Un **terminateur** \\r\\n

Le parsing en Go avec un `bufio.Reader` est super propre — un `switch` sur le premier caractère et le tour est joué.

## La concurrence en Go

Redis est **single-threaded** pour le traitement des commandes, mais utilise l'**I/O multiplexing**. En Go, c'est naturel grâce aux **goroutines** :

```go
func handleConnection(conn net.Conn) {
    defer conn.Close()
    reader := bufio.NewReader(conn)
    for {
        cmd, err := parseCommand(reader)
        if err != nil {
            return
        }
        response := executeCommand(cmd)
        conn.Write(response)
    }
}
```

Chaque connexion client a sa propre goroutine — Go gère le multiplexing tout seul.

## La réplication master-slave

La partie la plus intéressante : la **réplication**. Un slave Redis se connecte au master et :

1. Envoie `PING` pour vérifier la connexion
2. Envoie `REPLCONF listening-port <port>`
3. Envoie `PSYNC ? -1` pour demander le snapshot complet (RDB)
4. Reçoit les commandes en streaming (propagation)

Implémenter ça m'a fait comprendre pourquoi Redis est dit **"eventually consistent"** — le slave peut être légèrement en retard sur le master.

## Ce que j'en retiens

- **Go est parfait pour ce genre de projet** — la stdlib réseau est excellente
- **Les protocoles textes sont underrated** — RESP est simple mais puissant
- **La réplication c'est dur** — gérer la synchronisation sans locks est un vrai défi
- **On apprend 10x plus en construisant qu'en lisant**

Prochaine étape : la persistance RDB en binaire ! 🔥
