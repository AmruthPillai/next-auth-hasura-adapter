<p align="center">
   <br/>
   <a href="https://next-auth.js.org" target="_blank"><img height="64px" src="https://next-auth.js.org/img/logo/logo-sm.png" /></a>&nbsp;&nbsp;&nbsp;&nbsp;<img height="64px" src="https://user-images.githubusercontent.com/1134738/184874569-9ebc18c4-aa3f-40cd-baa8-3663ff1f8c80.png" />
   <h3 align="center"><b>Hasura Adapter</b> - NextAuth.js</h3>
   <p align="center">
   Open Source. Full Stack. Own Your Data.
   </p>
   <p align="center" style="align: center;">
      <a href="https://www.npmjs.com/package/next-auth-hasura-adapter" target="_blank"><img src="https://img.shields.io/bundlephobia/minzip/next-auth-hasura-adapter/next" alt="Bundle Size"/></a>
      <a href="https://www.npmjs.com/package/next-auth-hasura-adapter" target="_blank"><img src="https://img.shields.io/npm/v/next-auth-hasura-adapter/next" alt="next-auth-hasura-adapter Version" /></a>
   </p>
</p>

## Overview

This is the Prisma Adapter for [`next-auth`](https://next-auth.js.org). This package can only be used in conjunction with the primary `next-auth` package. It is not a standalone package.

You can find the Prisma schema in the docs at [next-auth.js.org/adapters/prisma](https://next-auth.js.org/adapters/prisma).

## Getting Started

1. Install `next-auth` and `next-auth-hasura-adapter` as well as `hasura-cli`.

```sh
npm install next-auth next-auth-hasura-adapter hasura-cli
```

2. Install the Hasura CLI, and dive into the console

```sh
hasura init
cd hasura && hasura console
```

3. Add the NextAuth models to your database through SQL

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;

SET check_function_bodies = false;

CREATE TABLE public.accounts (
    id uuid DEFAULT public.gen_random_uuid() NOT NULL,
    type text NOT NULL,
    provider text NOT NULL,
    "providerAccountId" text NOT NULL,
    refresh_token text,
    access_token text,
    expires_at bigint,
    token_type text,
    scope text,
    id_token text,
    session_state text,
    oauth_token_secret text,
    oauth_token text,
    "userId" uuid NOT NULL,
    refresh_token_expires_in bigint
);

CREATE TABLE public.sessions (
    id uuid DEFAULT public.gen_random_uuid() NOT NULL,
    "sessionToken" text NOT NULL,
    "userId" uuid NOT NULL,
    expires timestamptz
);

CREATE TABLE public.users (
    id uuid DEFAULT public.gen_random_uuid() NOT NULL,
    name text,
    email text,
    "emailVerified" timestamptz,
    image text
);

CREATE TABLE public.verification_tokens (
    token text NOT NULL,
    identifier text NOT NULL,
    expires timestamptz
);

ALTER TABLE ONLY public.accounts
    ADD CONSTRAINT accounts_pkey PRIMARY KEY (id);

ALTER TABLE ONLY public.sessions
    ADD CONSTRAINT sessions_pkey PRIMARY KEY (id);

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_email_key UNIQUE (email);

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_pkey PRIMARY KEY (id);

ALTER TABLE ONLY public.verification_tokens
    ADD CONSTRAINT verification_tokens_pkey PRIMARY KEY (token);

ALTER TABLE ONLY public.accounts
    ADD CONSTRAINT "accounts_userId_fkey" FOREIGN KEY ("userId") REFERENCES public.users(id) ON UPDATE RESTRICT ON DELETE CASCADE;

ALTER TABLE ONLY public.sessions
    ADD CONSTRAINT "sessions_userId_fkey" FOREIGN KEY ("userId") REFERENCES public.users(id) ON UPDATE RESTRICT ON DELETE CASCADE;
```

You can also find this file in `src/data/nextauth.sql`.

4. Add this adapter to your `pages/api/[...nextauth].js` next-auth configuration object.

```js
import NextAuth from "next-auth";
import { HasuraAdapter } from "next-auth-hasura-adapter";

export default NextAuth({
  providers: [],
  adapter: HasuraAdapter({
    endpoint: HASURA_API_ENPOINT,
    admin_secret: HASURA_ADMIN_SECRET,
  }),
});
```

## Contributing

We're open to all community contributions! If you'd like to contribute in any way, please read our [Contributing Guide](https://github.com/nextauthjs/next-auth/blob/main/CONTRIBUTING.md).

## License

ISC
