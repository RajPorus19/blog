---
title: "How I rebuilt Redis from scratch (and what it taught me)"
description: "My Codecrafters Redis challenge experience in Go"
date: 2026-05-25
image: "/images/project-placeholder.svg"
tags:
  - "go"
  - "redis"
  - "learning"
  - "backend"
mathjax: false
---

## Why rebuild Redis?

Redis is everywhere. Cache, sessions, queues, rate limiting... But until you've **written** a Redis server, you don't truly understand how it works.

I took on the **Codecrafters** challenge: rebuild Redis from scratch in Go. Here's what I learned along the way.

## The RESP protocol

The first step was parsing the **RESP** (Redis Serialization Protocol). It's a simple but elegant text protocol:

```
*3\r\n$3\r\nSET\r\n$5\r\nmykey\r\n$5\r\nhello\r\n
```

Each command is represented by:
- A **type** (string, error, integer, bulk string, array)
- A **length** for bulk strings
- A **\\r\\n** terminator

Parsing in Go with a `bufio.Reader` is super clean — a `switch` on the first character and you're done.

## Concurrency in Go

Redis is **single-threaded** for command processing but uses **I/O multiplexing**. In Go, this comes naturally with **goroutines**:

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

Each client connection gets its own goroutine — Go handles multiplexing automatically.

## Master-slave replication

The most interesting part: **replication**. A Redis slave connects to the master and:

1. Sends `PING` to verify the connection
2. Sends `REPLCONF listening-port <port>`
3. Sends `PSYNC ? -1` to request the full snapshot (RDB)
4. Receives streaming commands (propagation)

Implementing this taught me why Redis is called **"eventually consistent"** — the slave can lag slightly behind the master.

## Key takeaways

- **Go is perfect for this kind of project** — the stdlib networking is excellent
- **Text protocols are underrated** — RESP is simple yet powerful
- **Replication is hard** — managing sync without locks is a real challenge
- **You learn 10x more by building than by reading**

Next up: binary RDB persistence! 🔥
