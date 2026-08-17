# Korean Setup

## APT

```bash
sudo apt update
sudo apt install language-pack-ko
sudo update-locale LANG=ko_KR.UTF-8
```

`Settings` → `Keyboard` → `Input Sources` → `Korean (Hangul)`

## DNF

```bash
sudo dnf install langpacks-ko ibus-hangul
```

`Settings` → `Keyboard` → `Input Sources` → `Korean (Hangul)`