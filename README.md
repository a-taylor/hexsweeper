# Hexsweeper

Minesweeper on a hexagonal grid, built in vanilla JS with Canvas. No dependencies, no build step.

## Play

Open `index.html` in a browser. Or serve it locally:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Controls

- **Left click** — reveal a cell
- **Right click** — flag a cell
- **Touch** — tap to reveal, long-press to flag

The first click is always safe — mines are placed after it, avoiding the clicked cell and its neighbours.

## License

MIT
