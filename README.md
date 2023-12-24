Гайд создания

# Установка doctave
```bash
sudo apt install cargo
cargo install --git https://github.com/Doctave/doctave --tag 0.4.1
```

# Инициализация проекта
```bash
doctave init
```

# Подготовка для деплоя github pages
```bash
touch docs/_include/.nojekyll

npm install -g gh-pages@3.0.0
```

# Деплой
```bash
doctave build --release
gh-pages -d site
```

# Дев запуск
```bash
doctave serve
```

# Настройка github
Переходим в настройки (https://github.com/ITcompressor/itcompressor.github.io/settings/pages) и указываем ветку `gh-pages`

# Для уменьшения размера картинок можно использовать **imagemagick**
```bash
convert image.png -scale 20% -size 24% image2.png
```