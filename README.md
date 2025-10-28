### nahtao97's init.lua

  Esta es una versión de Neovim adaptada a partir de las configuraciones y atajos que más útiles me resultaron en el día a día. La
  base se inspira en configuraciones como las de ThePrimeagen y LazyVim, pero todo está ajustado para mis flujos.

  Requisito recomendado: instalar [ripgrep](https://github.com/BurntSushi/ripgrep) para las búsquedas (`<leader>ps`).

  #### Gestor de plugins

  - `lazy.nvim` (routed desde `lua/nahtao97/lazy_init.lua:1`). Cada archivo bajo `lua/nahtao97/lazy/` o `lua/plugins/` describe un
  set de plugins autocargado por Lazy.

  #### Plugins instalados (lazy.nvim)

  _Productividad_
  - `nvim-lua/plenary.nvim`: librería utilitaria usada por varias extensiones.
  - `L3MON4D3/LuaSnip` + `rafamadriz/friendly-snippets`: snippets personalizables (`<C-s>…`).
  - `laytan/cloak.nvim`: oculta secretos en `.env`.

  _Navegación y búsqueda_
  - `nvim-telescope/telescope.nvim`: finder fuzzy (`<leader>pf`, `<C-p>`, etc.).
  - `ThePrimeagen/harpoon` (branch `harpoon2`): marcadores rápidos (`<leader>a`, `<leader>1-4`).
  - `folke/trouble.nvim`: lista de diagnósticos (`<leader>tt`, `[t`, `]t`).

  _LSP, formato y diagnóstico_
  - `neovim/nvim-lspconfig`, `williamboman/mason.nvim`, `mason-lspconfig.nvim`: instalación/registro de servidores (`lua_ls`,
  `rust_analyzer`, `gopls`, `zls`).
  - `hrsh7th/nvim-cmp` + fuentes (`cmp-nvim-lsp`, `cmp-buffer`, `cmp-path`, `cmp-cmdline`) y `cmp_luasnip`: autocompletado.
  - `stevearc/conform.nvim`: framework de formateo (listo para añadir formatters).
  - `j-hui/fidget.nvim`: UI minimal de progreso LSP.

  _EDICIÓN_
  - `numToStr/Comment.nvim`: toggle de comentarios (`gc`, `gb`, etc.; ver sección “Remaps comunes”).

  _Control de versiones_
  - `tpope/vim-fugitive`: git-in-vim (`<leader>gs`, `gu`, `gh`).
  - *Dependencias implícitas*: fugitive requiere git en PATH.

  _Undo e historial_
  - `mbbill/undotree`: árbol visual de undo (`<leader>u`).

  _Árbol sintáctico y resaltado_
  - `nvim-treesitter/nvim-treesitter` + parser extra para templ; resalta, indenta y gestiona archivos grandes.
 #### Temas y color

  `ColorMyPencils()` en `lua/nahtao97/lazy/colors.lua:1` limpia los grupos de highlight y deja `Normal`/`NormalFloat` sin fondo,
  aprovechando la paleta del emulador. Si quieres activar un tema específico vuelve a llamar a `vim.cmd.colorscheme(...)` desde
  esa función o desde los bloques de configuración de los temas.

  ---

  #### Remaps personalizados (`lua/nahtao97/remap.lua`, `init.lua`, `lua/nahtao97/lazy/*.lua`)

  > Nota: `<leader>` es la barra espaciadora. Para remaps dependientes de plugins externos se indica el requisito.

  **Básicos y navegación**

  - `vim.g.mapleader = " "` — establece Space como prefijo.

  - `<leader>pv` — abre el explorador nativo (`:Ex`).

    ```text
    Antes: Sin tree.
    Después de `<leader>pv`: Netrw muestra el listado de archivos en la ventana actual.

  - J / K en modo visual — mueve el bloque seleccionado una línea abajo/arriba y re-selecciona.

    Antes (selección):
    foo
    bar
    baz

    Después de `J` en visual:
    bar
    foo
    baz
  - J en modo normal — une la línea siguiente sin mover el cursor inicial.

    Antes:
    foo
    bar

    Después de `J` (cursor seguía encima de `foo`):
    foo bar
  - <C-d> / <C-u> — desplazan media pantalla y recentran el cursor (útil en archivos largos).

    Antes: cursor en la línea 120 sin centrado.
    Después de `<C-d>`: la línea 144 queda centrada; el contenido no cambia.
  - n / N — avanzan/retroceden búsquedas manteniendo la coincidencia centrada.

    Antes: patrón “error” encontrado, cursor al borde de la ventana.
    Después de `n`: nueva coincidencia centrada con contexto extra arriba/abajo.
  - Q — deshabilitado para evitar entrar en Ex-mode por accidente.
  - <C-c> en modo insert — actúa como <Esc>, ideal para quienes vienen de VSCode.

  Clipboard y edición guiada

  - <leader>p en visual — pega sin destruir el registro principal.

    Buffer:
    foo
    bar

    Registro "+" contiene: baz

    Selecciona foo y pulsa <leader>p ⇒

    Resultado:
    baz
    bar    (y el registro sigue conteniendo "baz")
  - <leader>y (normal/visual) y <leader>Y (normal) — copian al portapapeles del sistema.

    Antes: cursor en `return err`.
    Después de `<leader>Y`: la línea `return err` está en el portapapeles (`pbpaste`/`xclip`).
  - <leader>d — borra sin tocar el portapapeles (usa el registro negro _).

    Antes:
    defer cancel()

    Después de `<leader>d`: la línea desaparece y el portapapeles conserva su contenido previo.
  - <leader>s — lanza sustitución global con la palabra bajo el cursor prellenada.

    // Antes
    fmt.Printf("user: %s\n", name)
    fmt.Printf("user: %s\n", name)

    // Tras `<leader>s` y escribir `fmt.Println`...
    fmt.Println("user:", name)
    fmt.Println("user:", name)
  - <leader>x — chmod +x al archivo actual (ideal para scripts).

    Antes: script.sh sin permiso de ejecución.
    Después de `<leader>x`: `ls -l` muestra `-rwx` en lugar de `-rw-`.
  - <leader><leader> — recarga el archivo actual de configuración (:source %), útil tras editar tu init.

  Integraciones externas

  - <C-f> — abre tmux-sessionizer en una ventana nueva (tmux requerido).
  - <leader>vwm / <leader>svwm — inician/detienen sesiones compartidas de vim-with-me.

    Antes: sesión local.
    Después de `<leader>vwm`: aparece el prompt de “Enter session name” de vim-with-me.
  - <leader>vpp — abre ~/.dotfiles/nvim/.config/nvim/lua/theprimeagen/packer.lua.
  - <leader>mr — lanza CellularAutomaton make_it_rain.

  Formato y LSP (autocmd LspAttach en init.lua:22)

  - <leader>f — formatea usando vim.lsp.buf.format().

    // Antes
    if err!=nil {return err}

    // Después de `<leader>f`
    if err != nil {
        return err
    }
  - gd, K, <leader>vws, <leader>vd, <leader>vca, <leader>vrr, <leader>vrn, [d, ]d, <C-h> (insert) — navegación y acciones
    LSP estándar (definición, hover, símbolos de workspace, diagnóstico flotante, code actions, referencias, rename, avanzar/
    retroceder diagnósticos, ayuda de firma).

    // Antes: cursor sobre `DoThing(ctx)`.
    // Después de `gd`: abre la definición en la misma ventana/otra según `split`.

  Snippets (lua/nahtao97/lazy/snippets.lua:8)

  - <C-s>e — expande el snippet actual.
  - <C-s>; / <C-s>, — saltan entre campos.
  - <C-E> — rota por las opciones de un campo con alternativas.

    // Antes de `<C-s>e`: escribiste `fn` y pulsas `<Tab>` (trigger).
    // Después: aparece el snippet con placeholders; usa `<C-s>;` para pasar al siguiente.

  Accesos rápidos Harpoon (lua/nahtao97/lazy/harpoon.lua)

  - <leader>a / <leader>A — añade/preprende archivo a la lista.
  - <C-e> — abre el menú rápido.
  - <leader>1..<leader>4 — salta al marcador correspondiente.
  - <leader><C-h>, <leader><C-t>, <leader><C-n>, <leader><C-s> — reemplazan los slots 1-4 con el buffer actual.

    Antes: lista vacía.
    Tras `<leader>a` en `api/server.go` y `<leader>1`: `Harpoon[1]` apunta a `server.go`.

  Go helpers (lua/nahtao97/remap.lua:40)

  - <leader>ee — inserta if err != nil { return err }.

    // Antes
    result, err := svc.Do(ctx)

    // Después de `<leader>ee`
    result, err := svc.Do(ctx)
    if err != nil {
        return err
    }
  - <leader>ea — añade assert.NoError(err, "").

    // Antes en tests
    err := run()

    // Después
    err := run()
    assert.NoError(err, "")
  - <leader>ef — inserta log.Fatalf.

    // Antes
    cfg, err := load()

    // Después
    cfg, err := load()
    if err != nil {
        log.Fatalf("error: %s\n", err.Error())
    }
  - <leader>el — prepara un logger.Error.

    // Antes
    res, err := client.Call()

    // Después
    res, err := client.Call()
    if err != nil {
        .logger.Error("error", "error", err)
    }

  Telescope (lua/nahtao97/lazy/telescope.lua)

  - <leader>pf — busca archivos.
  - <C-p> — archivos git tracked.
  - <leader>pws / <leader>pWs — grep de la palabra/cWORD bajo cursor.
  - <leader>ps — grep libre con prompt “Grep >”.
  - <leader>vh — abre :help en Telescope.

    Antes: no sabes dónde está `handlers/user.go`.
    Después de `<leader>pf`: escribes “handler” y lo abres desde el picker.

  Trouble (lua/nahtao97/lazy/trouble.lua)

  - <leader>tt — muestra/oculta la lista de diagnósticos.
  - [t / ]t — navega errores dentro de Trouble.

    Antes: buffer con errores sin orden.
    Después de `<leader>tt`: panel lateral con los diagnósticos agrupados por archivo.

  Git Fugitive (lua/nahtao97/lazy/fugitive.lua)

  - <leader>gs — abre :Git status (split dedicado).
  - Dentro de fugitive:### nahtao97's init.lua (versión modificada)

  Esta es una versión de Neovim adaptada a partir de las configuraciones y atajos que más útiles me resultaron en el día a día. La
  base se inspira en configuraciones como las de ThePrimeagen y LazyVim, pero todo está ajustado para mis flujos.

  Requisito recomendado: instalar [ripgrep](https://github.com/BurntSushi/ripgrep) para las búsquedas (`<leader>ps`).
