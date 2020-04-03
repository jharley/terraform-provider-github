---
layout: "github"
page_title: "GitHub: github_actions_secret"
description: |-
  Creates and manages an Action Secret within a GitHub repository
---

# github_actions_secret

This resource allows you to create and manage GitHub Actions secrets within your GitHub repositories.
You must have write access to a repository to use this resource.

Secret values are encrypted using the [Go '/crypto/box' module](https://godoc.org/golang.org/x/crypto/nacl/box) which is
interoperable with [libsodium](https://libsodium.gitbook.io/doc/). Libsodium is used by Github to decrypt secret values. 

For the purposes of security, the contents of the `plaintext_value` field have been marked as `sensitive` to Terraform,
but it is important to note that **this does not hide it from state files**. You should treat state as sensitive always.
It is also advised that you do not store plaintext values in your code but rather populate the `plaintext_value`
using fields from a resource, data source or variable as, while encrypted in state, these will be easily accessible
in your code. See below for an example of this abstraction.

## Example Usage

```hcl
data "github_actions_public_key" "example_public_key" {
  repository = "example_repository"
}

resource "github_actions_secret" "example_secret" {
  repository       = github_actions_public_key.example_public_key.respository
  secret_name      = "example_secret_name"
  plaintext_value  = var.some_secret_string
  key {
    key_id     = github_actions_public_key.example_public_key.key_id
    public_key = github_actions_public_key.example_public_key.key
  }
}
```

## Argument Reference

The following arguments are supported:

* `repository`      - (Required) Name of the repository
* `secret_name`     - (Required) Name of the secret
* `plaintext_value` - (Required) Plaintext value of the secret to be encrypted
* `key_id`          - (Deprecated) ID if the key used for encryption.
* `public_key`      - (Deprecated) Actual key to be used in encryption 
* `key`             - (Optional) A block decribing a `github_actions_public_key`. Each `key` block (max of 1) consists of the fields documented below
___

The `key` block consists of:
* `key_id`          - (Required) ID if the key used for encryption.
* `public_key`      - (Required) Actual key to be used in encryption of the `plaintext_value`.

## Attributes Reference

* `created_at`      - Timestamp of secret's creation
* `updated_at`      - Timestamp of latest update to secret