
local url = "https://raw.githubusercontent.com/gugusantt7890-hash/Gutgygfyg/refs/heads/main/README.md"

local ok, resultado = pcall(function()
    return game:HttpGet(url)
end)

if ok then
    print("Download concluído. Caracteres:", #resultado)
    print(resultado:sub(1, 300))
else
    warn("Falha no download: " .. tostring(resultado))
end 
