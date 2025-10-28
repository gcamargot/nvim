### nahtao97's init.lua (version modificada)

Esta configuracion de Neovim reune los atajos y plugins que mas me sirven. Nacio de mezclar ideas de ThePrimeagen, LazyVim y ajustes propios, asi que no es un clon de ninguna.

#### Requisitos

- `ripgrep` para que las busquedas de Telescope (`<leader>ps`) sean rapidas.
- `tmux` y el script `tmux-sessionizer` si vas a usar `<C-f>`.
- `vim-with-me` si necesitas compartir sesiones (`<leader>vwm`).

#### Gestor de plugins

- `lazy.nvim` (ver `lua/nahtao97/lazy_init.lua`). Cada archivo en `lua/nahtao97/lazy/` y `lua/plugins/` aporta un spec que Lazy carga automaticamente.

#### Plugins incluidos (Lazy.nvim)

- **Productividad**: `plenary.nvim`, `LuaSnip` + `friendly-snippets`, `cloak.nvim`, `cellular-automaton.nvim`.
- **Busqueda y navegacion**: `telescope.nvim`, `harpoon`, `trouble.nvim`.
- **LSP y formato**: `nvim-lspconfig`, `mason.nvim`, `mason-lspconfig`, `cmp` + fuentes, `conform.nvim`, `fidget.nvim`.
- **Edicion**: `Comment.nvim`.
- **Git**: `vim-fugitive`.
- **Historial y foco**: `undotree`, `zen-mode.nvim`.
- **Sintaxis**: `nvim-treesitter` con parser extra para `templ`.
- **Apariencia**: `solarized.nvim` (tema actual), coleccion de temas (`all-themes.lua`), recarga en caliente (`omarchy-theme-hotreload.lua`), `snacks.nvim` (scroll animado desactivado).

#### Color y temas

`ColorMyPencils()` (`lua/nahtao97/lazy/colors.lua`) limpia los highlights, respeta los colores del terminal y deja `Normal`/`NormalFloat` transparentes. Cambia la llamada a `vim.cmd.colorscheme()` si queres forzar un esquema distinto.

---

#### Remaps personalizados

> `<leader>` es Space. Entre parentesis indico el archivo de origen.

**Configuracion base**

```lua
-- lua/nahtao97/remap.lua
vim.g.mapleader = " "
```

**Navegacion y edicion de texto**

- `<leader>pv` abre el explorador nativo (`:Ex`).

```lua
vim.keymap.set("n", "<leader>pv", vim.cmd.Ex)
```

```text
Antes: estas editando src/main.go.
Accion: <leader>pv
Despues: la ventana muestra Netrw con el listado del directorio actual.
```

- `J` / `K` en visual mueven bloques seleccionados y conservan la seleccion.

```lua
vim.keymap.set("v", "J", ":m '>+1<CR>gv=gv")
vim.keymap.set("v", "K", ":m '<-2<CR>gv=gv")
```

```text
Antes (seleccion):
first
second
third

Accion: pulsa J
Despues:
first
third
second
```

- `J` en normal une lineas sin "saltos" del cursor.

```lua
vim.keymap.set("n", "J", "mzJ`z")
```

```text
Antes:
return nil
err = check()

Accion: J
Despues:
return nil err = check()
El cursor vuelve al punto original gracias a mz/`z.
```

- `<C-d>` / `<C-u>` desplazan media ventana y centran el cursor.

```lua
vim.keymap.set("n", "<C-d>", "<C-d>zz")
vim.keymap.set("n", "<C-u>", "<C-u>zz")
```

```text
Accion: <C-d>
Resultado: la linea destino aparece centrada (`zz`).
```

- `n` / `N` mantienen centradas las busquedas.

```lua
vim.keymap.set("n", "n", "nzzzv")
vim.keymap.set("n", "N", "Nzzzv")
```

```text
Tras buscar "panic", usa n/N para ir al resultado siguiente o anterior y dejarlo centrado.
```

- `<C-c>` en insert equivale a `<Esc>`; `Q` queda deshabilitado para evitar entrar en Ex-mode.

```lua
vim.keymap.set("i", "<C-c>", "<Esc>")
vim.keymap.set("n", "Q", "<nop>")
```

**Clipboard y sustituciones**

- Pegar sin perder el registro principal: `<leader>p` en visual.

```lua
vim.keymap.set("x", "<leader>p", [["_dP]])
```

```text
Antes: registro "+" = "world", buffer = "hello".
Accion: selecciona "hello" + <leader>p
Despues: el texto pasa a "world" y el registro sigue siendo "world".
```

- Copiar al sistema: `<leader>y` / `<leader>Y`.

```lua
vim.keymap.set({ "n", "v" }, "<leader>y", [["+y]])
vim.keymap.set("n", "<leader>Y", [["+Y]])
```

```text
Accion: visual -> seleccion -> <leader>y
Resultado: el texto queda listo para pegar en otra app (xclip/pbpaste).
```

- Borrar sin tocar el portapapeles: `<leader>d`.

```lua
vim.keymap.set({ "n", "v" }, "<leader>d", '"_d')
```

```text
Accion: <leader>d sobre una linea
Resultado: se elimina usando el registro negro `_` y el portapapeles permanece intacto.
```

- Busqueda y reemplazo rapido: `<leader>s`.

```lua
vim.keymap.set("n", "<leader>s", [[:%s/\<<C-r><C-w>\>/<C-r><C-w>/gI<Left><Left><Left>]])
```

```go
// Antes
fmt.Printf("user: %s\n", name)
fmt.Printf("user: %s\n", name)

// Accion
-- cursor sobre fmt.Printf
-- <leader>s -> escribe fmt.Println

// Despues
fmt.Println("user:", name)
fmt.Println("user:", name)
```

- Hacer ejecutable el archivo actual: `<leader>x`.

```lua
vim.keymap.set("n", "<leader>x", "<cmd>!chmod +x %<CR>", { silent = true })
```

```text
Antes: script.sh sin permisos.
Accion: <leader>x
Despues: `ls -l` muestra `-rwx`.
```

- Recargar la configuracion abierta: `<leader><leader>`.

```lua
vim.keymap.set("n", "<leader><leader>", function()
  vim.cmd("so")
end)
```

```text
Accion: editas init.lua y pulsas <leader><leader>.
Resultado: la configuracion se recarga sin reiniciar Neovim.
```

**Integraciones externas**

- Reiniciar el servidor LSP actual (util con Zig): `<leader>zig`.

```lua
vim.keymap.set("n", "<leader>zig", "<cmd>LspRestart<cr>")
```

- Compartir sesion con `vim-with-me`: `<leader>vwm` / `<leader>svwm`.

```lua
vim.keymap.set("n", "<leader>vwm", function()
  require("vim-with-me").StartVimWithMe()
end)
vim.keymap.set("n", "<leader>svwm", function()
  require("vim-with-me").StopVimWithMe()
end)
```

```text
Accion: <leader>vwm
Resultado: aparece el prompt de vim-with-me para elegir un codigo de sesion.
```

- Abrir tmux-sessionizer: `<C-f>`.

```lua
vim.keymap.set("n", "<C-f>", "<cmd>silent !tmux neww tmux-sessionizer<CR>")
```

- Abrir el viejo packer.lua para referencia: `<leader>vpp`.

```lua
vim.keymap.set("n", "<leader>vpp", "<cmd>e ~/.dotfiles/nvim/.config/nvim/lua/theprimeagen/packer.lua<CR>")
```

- Lanzar la animacion ASCII: `<leader>mr`.

```lua
vim.keymap.set("n", "<leader>mr", "<cmd>CellularAutomaton make_it_rain<CR>")
```

**Formato, diagnosticos y navegacion por errores**

- Formateo con LSP: `<leader>f`.

```lua
vim.keymap.set("n", "<leader>f", vim.lsp.buf.format)
```

```go
// Antes
if err!=nil {return err}

// Despues de <leader>f
if err != nil {
    return err
}
```

- Quickfix y location list con centrado automatico.

```lua
vim.keymap.set("n", "<C-k>", "<cmd>cnext<CR>zz")
vim.keymap.set("n", "<C-j>", "<cmd>cprev<CR>zz")
vim.keymap.set("n", "<leader>k", "<cmd>lnext<CR>zz")
vim.keymap.set("n", "<leader>j", "<cmd>lprev<CR>zz")
```

```text
Tras ejecutar :make, recorre los errores con <C-k>/<C-j> y mantene el cursor centrado.
```

- Plantillas rapidas para Go.

```lua
vim.keymap.set("n", "<leader>ee", "oif err != nil {<CR>}<Esc>Oreturn err<Esc>")
vim.keymap.set("n", "<leader>ea", "oassert.NoError(err, \"\")<Esc>F\";a")
vim.keymap.set("n", "<leader>ef", "oif err != nil {<CR>}<Esc>Olog.Fatalf(\"error: %s\\n\", err.Error())<Esc>jj")
vim.keymap.set("n", "<leader>el", "oif err != nil {<CR>}<Esc>O.logger.Error(\"error\", \"error\", err)<Esc>F.;i")
```

```go
// Accion: <leader>ee
result, err := svc.Do(ctx)
if err != nil {
    return err
}
```

**Atajos anadidos al adjuntarse un LSP** (`init.lua`)

```lua
vim.keymap.set("n", "gd", function() vim.lsp.buf.definition() end, opts)
vim.keymap.set("n", "K", function() vim.lsp.buf.hover() end, opts)
vim.keymap.set("n", "<leader>vws", function() vim.lsp.buf.workspace_symbol() end, opts)
vim.keymap.set("n", "<leader>vd", function() vim.diagnostic.open_float() end, opts)
vim.keymap.set("n", "<leader>vca", function() vim.lsp.buf.code_action() end, opts)
vim.keymap.set("n", "<leader>vrr", function() vim.lsp.buf.references() end, opts)
vim.keymap.set("n", "<leader>vrn", function() vim.lsp.buf.rename() end, opts)
vim.keymap.set("i", "<C-h>", function() vim.lsp.buf.signature_help() end, opts)
vim.keymap.set("n", "[d", function() vim.diagnostic.goto_next() end, opts)
vim.keymap.set("n", "]d", function() vim.diagnostic.goto_prev() end, opts)
```

```text
Ejemplo: posicionate sobre Handle(ctx) y usa gd para saltar a la definicion o <leader>vrn para renombrar con soporte del servidor.
```

**Autocompletado (nvim-cmp)**

```lua
["<C-p>"] = cmp.mapping.select_prev_item(cmp_select)
["<C-n>"] = cmp.mapping.select_next_item(cmp_select)
["<C-y>"] = cmp.mapping.confirm({ select = true })
["<C-Space>"] = cmp.mapping.complete()
```

```text
Dentro del menu de autocompletado, <C-n>/<C-p> recorren entradas, <C-y> acepta la seleccion y <C-Space> abre el menu manualmente.
```

**Snippets (LuaSnip)**

```lua
vim.keymap.set({ "i" }, "<C-s>e", function() ls.expand() end, { silent = true })
vim.keymap.set({ "i", "s" }, "<C-s>;", function() ls.jump(1) end, { silent = true })
vim.keymap.set({ "i", "s" }, "<C-s>,", function() ls.jump(-1) end, { silent = true })
vim.keymap.set({ "i", "s" }, "<C-E>", function()
  if ls.choice_active() then
    ls.change_choice(1)
  end
end, { silent = true })
```

```text
Escribi el prefijo, pulsa <C-s>e para expandir y navega los placeholders con <C-s>; / <C-s>,.
```

**Telescope**

```lua
vim.keymap.set("n", "<leader>pf", builtin.find_files)
vim.keymap.set("n", "<C-p>", builtin.git_files)
vim.keymap.set("n", "<leader>pws", function()
  local word = vim.fn.expand("<cword>")
  builtin.grep_string({ search = word })
end)
vim.keymap.set("n", "<leader>pWs", function()
  local word = vim.fn.expand("<cWORD>")
  builtin.grep_string({ search = word })
end)
vim.keymap.set("n", "<leader>ps", function()
  builtin.grep_string({ search = vim.fn.input("Grep > ") })
end)
vim.keymap.set("n", "<leader>vh", builtin.help_tags)
```

```text
<leader>pf abre un listado de archivos, <leader>ps pide una cadena y la busca en todo el proyecto.
```

**Harpoon**

```lua
vim.keymap.set("n", "<leader>A", function() harpoon:list():prepend() end)
vim.keymap.set("n", "<leader>a", function() harpoon:list():add() end)
vim.keymap.set("n", "<C-e>", function() harpoon.ui:toggle_quick_menu(harpoon:list()) end)
vim.keymap.set("n", "<leader>1", function() harpoon:list():select(1) end)
vim.keymap.set("n", "<leader>2", function() harpoon:list():select(2) end)
vim.keymap.set("n", "<leader>3", function() harpoon:list():select(3) end)
vim.keymap.set("n", "<leader>4", function() harpoon:list():select(4) end)
vim.keymap.set("n", "<leader><C-h>", function() harpoon:list():replace_at(1) end)
vim.keymap.set("n", "<leader><C-t>", function() harpoon:list():replace_at(2) end)
vim.keymap.set("n", "<leader><C-n>", function() harpoon:list():replace_at(3) end)
vim.keymap.set("n", "<leader><C-s>", function() harpoon:list():replace_at(4) end)
```

```text
Marca el archivo actual con <leader>a y salta al slot 1 con <leader>1. Usa <C-e> para abrir el menu flotante.
```

**Zen Mode y UndoTree**

```lua
vim.keymap.set("n", "<leader>zz", function()
  require("zen-mode").setup({
    window = {
      width = 90,
      options = {},
    },
  })
  require("zen-mode").toggle()
  vim.wo.wrap = false
  vim.wo.number = true
  vim.wo.rnu = true
  ColorMyPencils()
end)

vim.keymap.set("n", "<leader>zZ", function()
  require("zen-mode").setup({
    window = {
      width = 80,
      options = {},
    },
  })
  require("zen-mode").toggle()
  vim.wo.wrap = false
  vim.wo.number = false
  vim.wo.rnu = false
  vim.opt.colorcolumn = "0"
  ColorMyPencils()
end)
```

```text
<leader>zz centra el buffer con numeros visibles. <leader>zZ quita numeros y la columna 80 para un enfoque total.
```

```lua
vim.keymap.set("n", "<leader>u", vim.cmd.UndotreeToggle)
```

```text
<leader>u abre/cierra el arbol de undo en un split lateral.
```

**Git con Fugitive**

```lua
vim.keymap.set("n", "<leader>gs", vim.cmd.Git)
vim.keymap.set("n", "gu", "<cmd>diffget //2<CR>")
vim.keymap.set("n", "gh", "<cmd>diffget //3<CR>")
```

Dentro de un buffer fugitive:

```lua
vim.keymap.set("n", "<leader>p", function()
  vim.cmd.Git("push")
end, opts)
vim.keymap.set("n", "<leader>P", function()
  vim.cmd.Git({ "pull", "--rebase" })
end, opts)
vim.keymap.set("n", "<leader>t", ":Git push -u origin ", opts)
```

```text
Ejemplo: abre <leader>gs, resuelve conflictos con gu/gh y empuja con <leader>p sin salir de Neovim.
```

**Trouble**

```lua
vim.keymap.set("n", "<leader>tt", function()
  require("trouble").toggle()
end)
vim.keymap.set("n", "[t", function()
  require("trouble").next({ skip_groups = true, jump = true })
end)
vim.keymap.set("n", "]t", function()
  require("trouble").previous({ skip_groups = true, jump = true })
end)
```

```text
<leader>tt abre la vista de diagnosticos y [t/]t navega entre entradas.
```

---

#### Remaps comunes de Neovim (sin personalizar)

Siguen vigentes los basicos:

- `dd`, `yy`, `p`, `P` para lineas.
- `ci"`, `ci'`, `ci(`, `ci{` para editar texto delimitado.
- `>`, `<`, `=` en modo visual para ajustar indentacion.
- `:sp`, `:vsp`, `<C-w>h/j/k/l`, `<C-w>=` para splits.
- `gg`, `G`, `0`, `$` para navegacion rapida.
- `u` / `<C-r>` para undo/redo.

```text
Ejemplo: selecciona un bloque con V, pulsa = y Neovim reindenta automaticamente el codigo.
```

---

#### Comandos utiles

- `:CloakToggle`, `:CloakEnable`, `:CloakDisable`.
- `:HarpoonMenu`, `:TroubleToggle`, `:UndotreeToggle`, `:ZenMode`.
- `:Lazy sync`/`update` para plugins, `:Mason` para administrar servidores LSP.

