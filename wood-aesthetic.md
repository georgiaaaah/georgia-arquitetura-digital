# Wood Aesthetic — CSS

Efeito de madeira em CSS puro, sem imagens. Duas orientações: **flancos verticais** (desktop) e **barras horizontais** (mobile/topo e base).

---

## Token

```css
:root {
  --wood:   #b5651d;
  --wood-w: 28px;
}
```

---

## Flancos verticais (desktop)

Implementados via `::before` (esquerdo) e `::after` (direito) no container.

```css
.device { position: relative; }

/* Base compartilhada — grain + iluminação vertical */
.device::before,
.device::after {
  content: "";
  position: absolute;
  top: 0; bottom: 0;
  width: var(--wood-w);
  background-color: var(--wood);
  background-image:
    repeating-linear-gradient(90deg,
      rgba(255,255,255,0.05) 0px,
      rgba(0,0,0,0.07)       1px,
      rgba(0,0,0,0.1)        3px,
      transparent            4px),
    linear-gradient(to bottom,
      rgba(255,255,255,0.32) 0%,
      rgba(0,0,0,0.15)       100%);
  z-index: 20;
}

/* Flanco esquerdo — sombra projetada para a direita */
.device::before {
  left: 0;
  border-right: 2px solid rgba(0,0,0,0.52);
  box-shadow:
    inset -3px 0 8px rgba(0,0,0,0.35),
    inset  2px 0 4px rgba(255,255,255,0.12),
    4px  0 14px rgba(0,0,0,0.28),
    2px  0  4px rgba(0,0,0,0.18);
}

/* Flanco direito — sombra projetada para a esquerda */
.device::after {
  right: 0;
  border-left: 2px solid rgba(0,0,0,0.52);
  box-shadow:
    inset  3px 0 8px rgba(0,0,0,0.35),
    inset -2px 0 4px rgba(255,255,255,0.12),
    -4px 0 14px rgba(0,0,0,0.28),
    -2px 0  4px rgba(0,0,0,0.18);
}
```

**Lógica de luz:**
- Grain em `90deg` → listras verticais no flanco
- `to bottom`: topo mais claro (luz vinda de cima), base levemente mais escura
- `inset` lateral interno: borda que encosta no chassi fica em sombra
- `inset` lateral externo: borda exterior pega reflexo de luz ambiente
- Box-shadow externo: sombra projetada sobre o chassi na direção correta

---

## Barras horizontais (mobile)

Aplicadas dentro de `@media (max-width: 680px)`, sobrescrevendo os flancos.

```css
@media (max-width: 680px) {
  :root { --wood-w: 0px; }

  /* Base compartilhada — reseta posicionamento vertical para horizontal */
  .device::before,
  .device::after {
    top: auto; bottom: auto;
    left: 0; right: 0;
    width: 100%; height: 20px;
    background-color: var(--wood);
  }

  /* Barra superior — luz direta de cima, sombra projetada para baixo */
  .device::before {
    top: 0;
    border-right: none;
    border-bottom: 2px solid rgba(0,0,0,0.55);
    background-image:
      repeating-linear-gradient(0deg,
        rgba(255,255,255,0.05) 0px,
        rgba(0,0,0,0.07)       1px,
        rgba(0,0,0,0.1)        3px,
        transparent            4px),
      linear-gradient(to bottom,
        rgba(255,255,255,0.38) 0%,
        rgba(0,0,0,0.18)       100%);
    box-shadow:
      inset 0  2px 4px rgba(255,255,255,0.15),
      0  4px 12px rgba(0,0,0,0.28),
      0  2px  4px rgba(0,0,0,0.22);
  }

  /* Barra inferior — sombra do chassi no topo, luz ambiente na base */
  .device::after {
    bottom: 0;
    border-left: none;
    border-top: 2px solid rgba(0,0,0,0.55);
    background-image:
      repeating-linear-gradient(0deg,
        rgba(255,255,255,0.05) 0px,
        rgba(0,0,0,0.07)       1px,
        rgba(0,0,0,0.1)        3px,
        transparent            4px),
      linear-gradient(to bottom,
        rgba(0,0,0,0.06)        0%,
        rgba(255,255,255,0.22)  100%);
    box-shadow:
      inset 0 -2px 4px rgba(255,255,255,0.12),
      0 -4px 12px rgba(0,0,0,0.28),
      0 -2px  4px rgba(0,0,0,0.22);
  }
}
```

**Lógica de luz:**
- Grain em `0deg` → listras horizontais na barra (veio ao longo do comprimento)
- Barra superior: `to bottom` bright→dark — face de cima pega luz, borda inferior em sombra
- Barra inferior: `to bottom` dark→bright — topo sombreado pelo chassi acima, borda inferior pega reflexo ambiente
- Box-shadow externo: superior projeta sombra para baixo; inferior projeta para cima

---

## Notas de implementação

- O container precisa de `position: relative`
- `overflow: hidden` no container clipa as sombras projetadas — manter desativado
- No mobile, `border-radius: 0` no container — as barras retas terminam melhor que arredondadas
- `--wood-w: 0px` no mobile zera todos os `calc(var(--wood-w) + ...)` dos painéis internos automaticamente
