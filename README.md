# 🚀 MCP WordPress & Wordstat Server

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/Horosheff/sofiya/releases/tag/v1.0.0)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![MCP](https://img.shields.io/badge/MCP-2025--03--26-orange.svg)](https://modelcontextprotocol.io)

A production-ready **Model Context Protocol (MCP) server** with OAuth 2.0, providing seamless integration between AI assistants (ChatGPT, Claude) and WordPress/Yandex Wordstat APIs.

## ✨ Features

### 🔐 Full OAuth 2.0 Support
- **PKCE** (Proof Key for Code Exchange) with S256
- **Dynamic Client Registration** (RFC 7591)
- Compatible with ChatGPT Connectors and Make.com
- Multi-user support with isolated connectors

### 🛠️ 25 Powerful Tools

#### WordPress (18 tools)
- **Posts**: Create, update, read, delete, search, bulk operations
- **Categories**: Full CRUD operations
- **Media**: Upload from file or URL, manage library
- **Comments**: Create, read, update, delete

#### Yandex Wordstat (7 tools)
- **Analytics**: Top requests, dynamics, geographic distribution
- **Management**: Token setup, user info, diagnostics
- **Data**: Regions tree, search trends

### 🌐 Real-Time Communication
- **SSE (Server-Sent Events)** transport
- **JSON-RPC 2.0** protocol
- Automatic reconnection and keepalive
- Per-user isolated sessions

## 🚀 Quick Start

### For End Users

1. **Register** at [https://mcp-kv.ru](https://mcp-kv.ru)
2. **Configure** your WordPress and/or Wordstat credentials
3. **Copy** your unique connector URL
4. **Add** to ChatGPT:
   - Go to Settings → Connectors → Add Connector
   - Enter URL: `https://mcp-kv.ru/.well-known/mcp.json`
   - Authorize access

### For Developers

```bash
# Clone repository
git clone https://github.com/Horosheff/sofiya.git
cd sofiya

# Backend setup
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
python init_db.py
uvicorn app.main:app --reload

# Frontend setup (new terminal)
cd frontend
npm install
npm run dev
```

Visit `http://localhost:3000` to access the dashboard.

## 📖 Documentation

### MCP Server Manifest
```
GET https://mcp-kv.ru/.well-known/mcp.json
```

Returns server metadata including OAuth endpoints and SSE URL.

### OAuth Flow
1. **Discovery**: Client fetches `/.well-known/oauth-authorization-server`
2. **Registration**: `POST /oauth/register` with client metadata
3. **Authorization**: User approves access at `/oauth/authorize`
4. **Token Exchange**: `POST /oauth/token` with authorization code + PKCE verifier
5. **Access**: Use bearer token to connect to `/mcp/sse`

### Available Tools

<details>
<summary><b>WordPress Tools (18)</b></summary>

#### Posts
- `wordpress_create_post` - Create a new blog post
- `wordpress_update_post` - Update existing post
- `wordpress_get_posts` - List posts with filters
- `wordpress_delete_post` - Delete a post
- `wordpress_search_posts` - Search by keywords
- `wordpress_bulk_update_posts` - Update multiple posts at once

#### Categories
- `wordpress_create_category` - Create new category
- `wordpress_get_categories` - List all categories
- `wordpress_update_category` - Update category
- `wordpress_delete_category` - Delete category

#### Media
- `wordpress_upload_media` - Upload file to media library
- `wordpress_upload_image_from_url` - Import image from URL
- `wordpress_get_media` - List media files
- `wordpress_delete_media` - Delete media file

#### Comments
- `wordpress_create_comment` - Add comment to post
- `wordpress_get_comments` - List comments
- `wordpress_update_comment` - Update comment
- `wordpress_delete_comment` - Delete comment

</details>

<details>
<summary><b>Wordstat Tools (7)</b></summary>

- `wordstat_set_token` - Configure API access token
- `wordstat_get_user_info` - Check account status and quota
- `wordstat_get_regions_tree` - Get available regions
- `wordstat_get_top_requests` - Popular search queries
- `wordstat_get_dynamics` - Query trends over time
- `wordstat_get_regions` - Geographic query distribution
- `wordstat_auto_setup` - Interactive configuration wizard

</details>

## 🏗️ Architecture

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   ChatGPT   │◄───────►│ Nginx Proxy  │◄───────►│   FastAPI   │
│  (Client)   │  HTTPS  │  (SSL Term)  │  HTTP   │  (Backend)  │
└─────────────┘         └──────────────┘         └──────┬──────┘
                                                          │
                        ┌──────────────┐         ┌───────▼──────┐
                        │   Next.js    │         │   SQLite     │
                        │  (Frontend)  │         │  (Database)  │
                        └──────────────┘         └──────────────┘
                               │
                        ┌──────▼───────┐         ┌──────────────┐
                        │  WordPress   │         │   Wordstat   │
                        │     API      │         │     API      │
                        └──────────────┘         └──────────────┘
```

## 🔧 Configuration

### Environment Variables

Create `.env` file in backend directory:

```env
# Database
DATABASE_URL=sqlite:///./app.db

# Security
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Server
BASE_URL=https://mcp-kv.ru

# External APIs (optional)
MCP_SERVER_URL=http://localhost:8080
```

### Nginx Configuration

See `DEPLOYMENT.md` for full Nginx setup with SSL.

Key configuration points:
- HTTPS termination with Let's Encrypt
- WebSocket/SSE support for `/mcp/sse`
- Authorization header forwarding
- Rate limiting and security headers

## 🛡️ Security

- ✅ **PKCE** prevents authorization code interception
- ✅ **Per-user connectors** isolate data access
- ✅ **Encrypted storage** for API credentials
- ✅ **JWT tokens** with expiration
- ✅ **HTTPS** with modern TLS (1.2+)
- ✅ **Content Security Policy** headers
- ✅ **Rate limiting** on sensitive endpoints

## 📊 Tech Stack

| Component | Technology |
|-----------|-----------|
| Backend | Python 3.9+, FastAPI |
| Frontend | Next.js 14, TypeScript |
| Database | SQLite with SQLAlchemy |
| Web Server | Nginx 1.24.0 |
| SSL | Let's Encrypt (Certbot) |
| Auth | OAuth 2.0 + PKCE, JWT |
| Protocol | MCP (SSE + JSON-RPC) |

## 🤝 Integration Examples

### ChatGPT

```
1. Settings → Connectors → Add Connector
2. URL: https://mcp-kv.ru/.well-known/mcp.json
3. Authorize access
4. Start using tools in your chats!
```

### Make.com

```
1. Create new scenario
2. Add MCP module
3. Enter connector URL
4. Authenticate with OAuth
5. Select tool and configure parameters
```

### API (Python)

```python
import httpx

# Authenticate
response = httpx.post(
    "https://mcp-kv.ru/api/auth/login",
    json={"email": "user@example.com", "password": "password"}
)
token = response.json()["access_token"]

# Call tool
response = httpx.post(
    f"https://mcp-kv.ru/mcp/sse/{connector_id}",
    headers={"Authorization": f"Bearer {token}"},
    json={
        "jsonrpc": "2.0",
        "method": "tools/call",
        "params": {
            "name": "wordpress_get_posts",
            "arguments": {"per_page": 5}
        },
        "id": 1
    }
)
```

## 📈 Roadmap

- [ ] Additional CMS integrations (Joomla, Drupal)
- [ ] More analytics providers (Google Analytics, Yandex Metrica)
- [ ] WebSocket transport option
- [ ] Rate limiting per user
- [ ] Admin dashboard
- [ ] Multi-language support
- [ ] Tool usage analytics
- [ ] Custom tool builder

## 🐛 Bug Reports & Feature Requests

Please use [GitHub Issues](https://github.com/Horosheff/sofiya/issues) to report bugs or request features.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Model Context Protocol](https://modelcontextprotocol.io) specification
- [FastAPI](https://fastapi.tiangolo.com) framework
- [Next.js](https://nextjs.org) React framework
- [WordPress REST API](https://developer.wordpress.org/rest-api/)
- [Yandex Wordstat API](https://yandex.ru/dev/direct/doc/start/about.html)

## 📞 Support

- **Documentation**: See `DEPLOYMENT.md` for deployment guide
- **Issues**: [GitHub Issues](https://github.com/Horosheff/sofiya/issues)
- **Live Demo**: [https://mcp-kv.ru](https://mcp-kv.ru)

---

**Made with ❤️ for the MCP community**

[⬆ Back to Top](#-mcp-wordpress--wordstat-server)

<!-- SAIFER_REPO_PASSPORT_BEGIN: #435 -->
## Паспорт

- **Что это:** A production-ready **Model Context Protocol (MCP) server** with OAuth 2.0, providing seamless integration between AI assistants (ChatGPT, Claude) and WordPress/Yandex Wordstat APIs..
- **Статус:** не знаю (работает / пауза / заморожено ❄️ / брошено — нет runtime-проверки).
- **Где работает:** не знаю (сервер/ПК/сайт/облако не подтверждены).
- **Как запустить или открыть:** [репозиторий](https://github.com/SaiferSMART/my_sofiya); запуск приложения — не знаю (ищите инструкции в README).
- **Связанные открытые PR:** [актуальный список](https://github.com/SaiferSMART/my_sofiya/pulls?q=is%3Apr+is%3Aopen).
- **Связанные открытые Issue:** [актуальный список](https://github.com/SaiferSMART/my_sofiya/issues?q=is%3Aissue+is%3Aopen).
- **Дата последнего изменения GitHub:** 2025-10-14T22:48:44Z (поле `pushed_at`, любая ветка; не равно проверке эксплуатации).
- **Кто ведёт:** не знаю; владелец репозитория GitHub — `SaiferSMART`.
- **Вопрос к владелице:** подтвердить статус, площадку, точку входа и фактического ведущего.
- **Задание:** [Issue №435](https://github.com/SaiferSMART/saifer-ai-ops/issues/435).
<!-- SAIFER_REPO_PASSPORT_END -->
