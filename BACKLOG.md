# Беклог

## Онбординг: пояснення призначення кожної сторінки

Реалізувати вступний тур застосунком — коротко пояснити навіщо потрібна кожна
вкладка/сторінка і що на ній робити: Головна, План (План А/Б, розминка,
мікроцілі), SOS (швидка підказка в складний момент матчу), Матчі (запис
результатів і звіт), Профіль (дані, ракетка, удари, самопочуття — навіщо це
запитується і як впливає на поради). Показувати при першому вході після
створення профілю, з можливістю пропустити й переглянути пізніше (напр., кнопка
«?» на кожному екрані).

## Хмарна синхронізація + вхід через Google (відкладено)

У `index.html` вже є код для опціональної хмарної синхронізації через Supabase
(вхід через Google, дані профілю/турнірів доступні з будь-якого пристрою).
Зараз він неактивний — увімкнеться сам, як тільки будуть підставлені реальні
`SUPA_URL` і `SUPA_ANON_KEY` (зараз там заглушки `YOUR-PROJECT` / `YOUR-ANON-PUBLIC-KEY`).
До того часу сайт працює як і раніше — лише локальне збереження (localStorage).

Що знадобиться, коли повернемось до цього:

1. Створити проєкт на supabase.com
2. Виконати в SQL Editor:
   ```sql
   create table public.user_data (
     user_id uuid primary key references auth.users(id) on delete cascade,
     data jsonb not null default '{}'::jsonb,
     updated_at timestamptz not null default now()
   );
   alter table public.user_data enable row level security;
   create policy "select own" on public.user_data for select using (auth.uid() = user_id);
   create policy "insert own" on public.user_data for insert with check (auth.uid() = user_id);
   create policy "update own" on public.user_data for update using (auth.uid() = user_id);
   ```
3. Authentication → Providers → Google → Enable (потрібен OAuth Client ID/Secret
   з Google Cloud Console, redirect URI підкаже сама сторінка Supabase)
4. Authentication → URL Configuration → Site URL і Redirect URLs = адреса деплою
5. Дати Claude значення Project URL і anon public key з Project Settings → API
