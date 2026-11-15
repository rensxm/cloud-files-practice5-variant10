# Практикалық жұмыс №6

**10-нұсқа: файлдық сақтау.** Node.js/Express және PostgreSQL негізіндегі REST API.

## Маршруттар

| Әдіс | Маршрут | Нәтиже |
|---|---|---|
| GET | / | Қызметтің іске қосылғаны туралы мәтін |
| GET | /info | Нұсқа және DATABASE_URL орнатылғанының құпиясыз белгісі |
| GET | /api/files | Барлық файл жазбалары, HTTP 200 |
| GET | /api/files/:id | Бір файл, HTTP 200; табылмаса HTTP 404 |
| POST | /api/files | Жаңа файл метадеректері, HTTP 201 |
| GET | /files | №5 жұмыстағы маршрутпен үйлесімді тізім |

Қолданба файлдардың метадеректерін сақтайды. Файлдың өзін жүктеу осы тапсырмаға кірмейді.

## Файлдар

- `server.js`: маршруттар, кіріс деректерін тексеру және қате өңдеу.
- `db.js`: `DATABASE_URL` арқылы PostgreSQL қосылымдарының пулы.
- `schema.sql`: `files` кестесі және үш бастапқы жазба.
- `init-db.js`: кестені транзакция ішінде дайындау. Ол npm start кезінде автоматты орындалады.
- `package.json`, `package-lock.json`: тәуелділіктер мен іске қосу командалары.
- `.env.example`: тек айнымалылардың үлгісі, нақты құпия қосылым жолы жоқ.
- `.gitignore`: жергілікті құпиялар мен тәуелділіктерді Git-тен алып тастайды.
- `render.yaml`: тегін Web Service үшін дайын конфигурация.
- `requests.http`: сұраныс үлгілері.
- `local-check.json`: нақты жергілікті тексеру нәтижелері.

## PostgreSQL кестесі

`files(id SERIAL PRIMARY KEY, name VARCHAR(255) UNIQUE NOT NULL,
content_type VARCHAR(127) NOT NULL, size_bytes INTEGER NOT NULL)`.

Өлшем теріс болмауы тиіс. Атау қайталанса API HTTP 409 қайтарады.
Бастапқы жазбалар: `дәріс.pdf`, `таныстырылым.pptx`, `конспект.txt`.
Инициализацияны қайта орындағанда бастапқы атаулар қайталанып қосылмайды.

## Жергілікті іске қосу

Node.js 20 немесе жаңарақ нұсқасы, npm және PostgreSQL қажет.
PostgreSQL ішінде өз пайдаланушыңызға тиесілі база жасаңыз.
PowerShell терезесінде жоба папкасына өтіп орындаңыз:

```powershell
npm install
$env:DATABASE_URL = Read-Host 'PostgreSQL құпия қосылым жолын енгізіңіз'
$env:DATABASE_SSL = 'disable'
$env:STUDENT_VARIANT = '10'
npm run db:init
npm start
```

`disable` жергілікті TLS қолданбайтын PostgreSQL үшін көрсетілген.
Бұлттық база үшін провайдердің TLS талабын орындаңыз; сыртқы қосылымда
`DATABASE_SSL` айнымалысын алып тастасаңыз сертификат тексеруі қосылады.
Егер провайдер `sslmode` параметрі бар дайын URL берсе, `pg` сол URL параметрін
қолданады. Провайдердің қосылым жолын өзгертпей, ресми нұсқаулығын ұстаныңыз.
`.env.example` автоматты түрде оқылмайды: айнымалыларды ортаға өзіңіз орнатасыз.
`PORT` берілмесе 3000 қолданылады. Тоқтату үшін Ctrl+C басыңыз.

## API тексеру

```powershell
Invoke-RestMethod http://localhost:3000/api/files
Invoke-RestMethod http://localhost:3000/api/files/1
$file = @{ name = 'зертхана.txt'; content_type = 'text/plain'; size_bytes = 2048 }
$json = $file | ConvertTo-Json
Invoke-RestMethod http://localhost:3000/api/files -Method Post -ContentType 'application/json; charset=utf-8' -Body ([Text.Encoding]::UTF8.GetBytes($json))
```

POST сұранысы `id` және жіберілген өрістері бар жаңа объектіні қайтарады.
Бірдей атаумен POST сұранысын қайталау HTTP 409 береді.
Жаңа объектінің `id` мәнімен GET сұранысын орындап, оның сақталғанын тексеріңіз.

## GitHub және бұлтта жариялау

1. GitHub-та бос `cloud-files-api-variant10` репозиторийін жасаңыз.
2. Осы папканың файлдарын Git арқылы жүктеңіз; `.env` және құпия URL жүктелмейді.
3. Render-де тегін PostgreSQL базасын жасаңыз. Қызмет пен базаны бір өңірге орналастырыңыз.
4. Жаңа Web Service құрып, GitHub репозиторийін қосыңыз.
5. Branch: `main`; Runtime: `Node`; Plan: `Free`.
6. Build Command: `npm install`; Start Command: `npm start`. Іске қосу кезінде кесте автоматты дайындалады.
7. Environment ішінде `DATABASE_URL` нақты құпия мәнін және `STUDENT_VARIANT=10` орнатыңыз.
8. Render ішкі қосылымы TLS талап етпесе `DATABASE_SSL=disable` орнатыңыз. Сыртқы TLS қосылымы үшін провайдер талабын қолданыңыз.
9. Deployment аяқталғаннан кейін жария URL арқылы үш API сұранысын тексеріңіз.
10. Нәтижелерді, GitHub сілтемесін және жария URL-ді есепке енгізіңіз.

Тек тегін жоспарды таңдаңыз. Карта міндетті болып шықса, оқытушы мақұлдаған
тегін PostgreSQL және PaaS баламасын пайдаланыңыз.

## Қате қосылымды зерттеу

Тек осы оқу жобасының Environment баптауында `DATABASE_URL` мәніндегі база
атауын уақытша жоқ базаға ауыстырыңыз. Нақты құпия мәнді чатқа немесе есепке
жазбаңыз. Қызметті қайта іске қосып, GET /api/files сұранысын жіберіңіз.
Күтілетін HTTP жауабы: 503. Runtime log-та `DB_QUERY_ERROR 3D000` көрінеді.
Дұрыс URL-ді қалпына келтіріп, қайта іске қосыңыз; GET сұранысы 200 беруі тиіс.
Жергілікті `local-check.json` файлы бұлттық тексерудің орнына жүрмейді.

## Ресми дереккөздер

- https://node-postgres.com/features/queries
- https://node-postgres.com/features/ssl
- https://render.com/docs/deploy-node-express-app
- https://render.com/docs/free
