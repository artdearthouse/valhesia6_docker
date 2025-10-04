# Valhelsia 6 Docker Server

Dockerized Minecraft server with Valhelsia 6 modpack and automatic backups.

## Features

- 🎮 Valhelsia 6 modpack with automatic download via CurseForge
- 💾 Automatic hourly backups with rotation
- ☕ Java 21 GraalVM for optimal performance
- 🐳 Simple deployment via Docker Compose

## Requirements

- Docker and Docker Compose
- Minimum 4GB RAM
- CurseForge API key (free)

## Quick Start

### 1. Get CurseForge API Key

1. Register at [CurseForge Console](https://console.curseforge.com/)
2. Create a new API key
3. Copy the key for the next step

### 2. Configure Environment Variables

Create `.env` file in project root:

```bash
cp .env.example .env
```

Edit `.env` and add your API key:

```env
CF_API_KEY=$$2a$$10$$...your_key...
```

⚠️ **Important**: Double the dollar signs in the key (`$` → `$$`)

### 3. Start the Server

```bash
docker compose up -d
```

Server will be available on port `25565`.

## Configuration

### Server Settings

Configurable in `docker-compose.yml`:

- `MEMORY`: Allocated memory (default 3.5G)
- `RCON_PASSWORD`: Password for RCON connection (used for backups)

### Backup Settings

The `backup` container automatically creates backups:

- **Frequency**: Every hour
- **Retention**: 7 days (old backups are pruned automatically)
- **Location**: `./backups/`
- **Format**: tar.gz archives with timestamps

Parameters can be modified in `docker-compose.yml`:

```yaml
BACKUP_INTERVAL: "1h"      # Backup interval
PRUNE_BACKUPS_DAYS: 7      # Days to keep backups
INITIAL_DELAY: 5m          # Delay before first backup
```

### Manual Backup

To create a backup outside the schedule:

```bash
docker compose exec backup backup now
```

## Project Structure

```
valhesia6_docker/
├── docker-compose.yml   # Container configuration
├── .env                 # Environment variables
├── data/               # Minecraft server data
└── backups/            # Backup files
```

## Server Management

### View Logs

```bash
# Minecraft server logs
docker compose logs -f mc-valhesia6

# Backup logs
docker compose logs -f backup
```

### Stop Server

```bash
docker compose down
```

### Update Modpack

The modpack updates automatically on restart thanks to `CF_FORCE_SYNCHRONIZE: "true"`.

## Restore from Backup

1. Stop the server:
   ```bash
   docker compose down
   ```

2. Find the desired backup in `./backups/`

3. Restore the data:
   ```bash
   tar -xzvf backups/world-YYYYMMDD-HHMMSS.tar.gz -C data/
   ```

4. Start the server:
   ```bash
   docker compose up -d
   ```

## Troubleshooting

### Server Won't Start

- Check CurseForge API key is correct
- Ensure port 25565 is not in use
- Check logs: `docker compose logs mc-valhesia6`

### Backups Not Created

- Verify RCON is enabled and passwords match
- Check backup container logs: `docker compose logs backup`
- Ensure write permissions for `./backups/` directory

## Technologies

- [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server) - Minecraft server Docker image
- [itzg/mc-backup](https://github.com/itzg/docker-mc-backup) - Automatic backup system
- [Valhelsia 6](https://www.curseforge.com/minecraft/modpacks/valhelsia-6) - Modpack

## License

MIT