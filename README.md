# Pelican

A Neovim plugin for interacting with [Simon Willison's LLM CLI tool](https://github.com/simonw/llm).

## Features

- Basically a simple scratch buffer plugin + a mechanism for streaming results from the LLM CLI tool.

## Requirements

- Neovim 0.5.0 or later
- [llm](https://github.com/simonw/llm) installed and configured

## Configuration

```lua
require('pelican').setup({
  llm_path = "llm",  -- Path to the llm executable
})
```

## Usage

### Commands

- `:Scratch` - Create a new Markdown scratch file with a timestamp based name.
- `:OpenLatestScratch` - Open last modified file in scratch folder.
- `:LLM` - Call LLM with the current buffer or visual selection as input. Streams output to a new scratch buffer in a vertical split window. Can also take command line args as expected. e.g. `:LLM -m claude-3.7-sonnet`
- `:LLMLogs` - Outputs the result of `llm logs` to a new scratch buffer. Also takes command line args as expected e.g. `:LLMLogs -r`
- `:YankCodeBlock` - Yank buffer/selection as Markdown code block.
- `:SelectCodeBlock` - If the cursor is within a Markdown code block, visually select the content of the code block.

## Example Keymap

```vim
" Paste buffer/visual selection as code block in new scratch file
noremap <leader>cn :YankCodeBlock<CR>:Scratch<CR>pGo<Esc>
" Paste buffer/visual selection as code block to end of latest scratch file
noremap <leader>cp :YankCodeBlock<CR>:OpenLatestScratch<CR>Go<Esc>pGo<Esc>
" Paste buffer/visual selection into last code block in latest scratch file.
vnoremap <leader>ca y:OpenLatestScratch<CR>G:call search('^\s*```\s*$', 'b')<CR>P
nnoremap <leader>ca :%y<CR>:OpenLatestScratch<CR>G:call search('^\s*```\s*$', 'b')<CR>P
" Open ex commandline ready to type cli flags for LLM
nnoremap <leader>llm :LLM<space>
vnoremap <leader>llm :<C-u>LLM<space>
augroup PelicanMarkdown
    au!
    " yank content of code block
    au FileType markdown nnoremap <buffer> <C-i> :SelectCodeBlock<CR>y
    " send buffer/visual selection to sonnet 3.7 thinking
    au FileType markdown nnoremap <buffer> <C-g> :LLM -m claude-3.7-sonnet -o thinking_budget 32000<CR>
    au FileType markdown vnoremap <buffer> <C-g> :<C-u>LLM -m claude-3.7-sonnet -o thinking_budget 32000<CR>
augroup end
```


#### Note

Files in the scratch folder autosave with a 1 second debounce.


## License

MIT
