# Regras do Firestore

O app grava em dois lugares do Firestore:

| Caminho | O que é |
|---|---|
| `users/{uid}` | O estado do estudo (sessões, plano da semana, configurações…) — campo `data` |
| `users/{uid}/backups/{semana}` | Snapshots semanais automáticos (últimos 12) e backups "pré-restauração" |

As regras do Firestore precisam liberar **os dois**. Regras que cobrem só `users/{uid}` **não valem para as
subcoleções** — o resultado é que o app sincroniza normalmente, mas os backups semanais **nunca são criados**
e "Minha conta → Backups semanais" mostra erro.

## Regras recomendadas

Console do Firebase → **Firestore Database** → aba **Regras** → substitua por:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Cada usuário lê e grava SOMENTE os próprios dados (documento + subcoleções)
    match /users/{uid}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

Clique em **Publicar**. Não precisa alterar o código nem reimplantar o app.

## Como conferir

1. Recarregue o app e abra **Minha conta**.
2. Em "Backups semanais" deve aparecer a lista (ou "Nenhum backup ainda — o primeiro é criado no próximo
   salvamento"). Após registrar qualquer sessão, o snapshot da semana é criado.
3. Se aparecer *"Sem permissão para os backups"*, as regras ainda não foram publicadas (ou o caminho está errado).
