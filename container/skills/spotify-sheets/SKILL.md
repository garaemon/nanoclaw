---
name: spotify-sheets
description: Search and browse Spotify liked songs and followed artists from Google Sheets. Supports daily digest of new likes and artist lookup for live event searches.
allowed-tools: Bash(spotify-sheets:*)
---

# Spotify Sheets

Search and browse the user's Spotify library stored in Google Sheets.

## Quick start

```bash
spotify-sheets list-songs                     # List recent liked songs (default 20)
spotify-sheets list-songs --limit 50          # List more songs
spotify-sheets list-artists                   # List followed artists
spotify-sheets search "Radiohead"             # Search songs and artists
spotify-sheets search --songs "math rock"     # Search only songs
spotify-sheets search --artists "indie"       # Search only artists
spotify-sheets new-since 2026-03-20           # Songs liked since a date
spotify-sheets artist-detail "NABOWA"         # Artist info + their liked songs
spotify-sheets genres                         # List all genres with counts
spotify-sheets stats                          # Library overview and top genres
```

## Commands

| Command | Description |
|---------|-------------|
| `list-songs [--limit N]` | List recent liked songs (newest first) |
| `list-artists` | List all followed artists |
| `search [--songs\|--artists] <query>` | Full-text search across title, artist, album, genre |
| `new-since <YYYY-MM-DD>` | Songs liked since a date, with unique artist summary |
| `artist-detail <name>` | Show artist profile and all their liked songs |
| `genres` | Genre frequency across songs and artists |
| `stats` | Library totals, date range, top 10 genres |

## When to use

- User asks about their Spotify library, liked songs, or followed artists
- User wants to search for a song or artist in their collection
- User asks about genres or music taste
- User wants a link to a song or artist on Spotify

## Scheduled task patterns

This skill is designed for use with scheduled tasks:

### Daily new-likes digest
Use `new-since` with yesterday's date to find newly liked songs, then summarize the new artists.

```
spotify-sheets new-since $(date -d yesterday +%Y-%m-%d)
```

### Artist live event lookup
Use `list-artists` or `artist-detail` to get artist names, then use a web search tool to find upcoming concerts.
