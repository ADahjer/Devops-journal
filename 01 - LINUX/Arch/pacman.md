# pacman (Arch Linux Package Manager)

## What is pacman?
Pacman is the command-line **package manager for Arch Linux**, responsible for installing, updating, removing, and querying software packages while managing dependencies.  
Pacman facilitates keeping your system up-to-date and clean.

---

##  Basic Commands
- `sudo pacman -Syu` → Update system
- `sudo pacman -S <package>` → Install a package
- `sudo pacman -R <package>` → Remove a package
- `sudo pacman -Rs <package>` → Remove a package and unused dependencies

> [!NOTE]
> When installing packages in Arch, avoid refreshing the package list without upgrading the system
> Do `pacman -Syu <package>` instead of `pacman -Sy <package>`