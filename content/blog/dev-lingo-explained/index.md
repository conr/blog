---
title: 5 Examples of Software Engineering Terms Explained
date: "2021-01-16T11:00:00.169Z"
---

## Intro
In the business of developing software we all hear and subsequently begin to use words and phrases that are alien to an outsider. In this post I want to explain some of them.

## Nomenclature

"There are only two hard things in Computer Science: cache invalidation and naming things."

-- Phil Karlton

What Mr. Karlton was referring to in the latter part of this famous quote was nomenclature. Nomenclature is the devising or choosing of names for things. We do that a lot when writing applications and a lot of time has been spent trying to come up with the best ways for doing so.

## Idempotent

We can say an operation is idempotent when it is performed more than once and the additional operations have no affect on the state of the system. Take for example, in the context web development, when a user deletes a file and the request succeeds resulting in an HTTP 201 response code. If the user were to issue the exact same request again, the server would respond again with a 201 and its state would not be affected.

An example of a non-idempotent operation could be a bad implementation of a payment system. Let's say we have a buggy client that ends up POSTing to the same `/create-transaction` endpoint twice with the same request body. The server receives two requests and ends up creating two transactions when the intent was only to create one. This request is therefore not idempotent. It can be made idempotent by sending a unique identifier for the request to allow the server to correctly identify and discard the second request as an unnecessary duplicate.

## Ephemeral

Meaning short lived, temporary, or transient. I've heard ephemeral used to describe short lived jobs that appear in a Kubernetes cluster and disapper once compelted, ephemeral caches, and the ephmeral storage attached to node which is only present during the lifetime of said nodes in a K8s cluster.

## Immutable

Immutable is simply an adjective to denote something as unchangeable. In the context of programming, an immutable object or variable is one that cannot be updated or have its internal state changed once instantiated. An example of this would be a string in languages like Java and Python.

## Delimiter

A delimiter is that which specifies the boundary between different regions in text. If you have ever split string on a certain character of sequence of characters, then you've made use of a delimiter.
