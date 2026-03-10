# TP4-Softwar-Evolution

## Section 1.2.4

### Question 1 : 
The probability is virtually zero. SHA-256 is designed to be collision-resistant; even with the massive computing power of the entire Bitcoin network, it would take roughly 18 billion years to have a 50% chance of finding a single collision


### Question 3 : 
No, this is not recommended for strict reproducibility. TAR archives are not inherently reproducible because they record build-specific metadata such as timestamps, file ownership, and permissions, which vary across different systems and build environments. To make a tarball reproducible, one must explicitly normalize or strip this metadata using specific flags

## Section 2.2.1

### Question 1 :
Metadata can be found using ls -l (size, permissions) and file hello-world (architecture, e.g., x86_64). Standard compilation generates intermediate object files (.o), though they are often deleted automatically by the compiler unless specified otherwise

### Question 2 :
No, the binary hash changes because the __DATE__ and __TIME__ macros embed the exact moment of compilation into the executable

### Questoin 3 : 
Yes, because the timestamp strings are fixed within the compiled machine code

### challenge : 
By defining the SOURCE_DATE_EPOCH environment variable (e.g., export SOURCE_DATE_EPOCH=1700000000), we provide a fixed reference point for the compiler. Modern versions of GCC recognize this variable and use it to replace the non-deterministic values of the __DATE__ and __TIME__ macros. This ensures that the resulting binary remains bit-for-bit identical across multiple compilations, effectively achieving build-time reproducibility despite the use of timestamps.

## Section 3.3.1

### Question 1 : 
It should accept two parameters: the seed and the number of iterations. Controlling the seed guarantees the pseudo-random number sequence is deterministic, and controlling the number of iterations ensures the exact same amount of calculation is performed, making the runtime fully reproducible. (Build-time reproducibility was achieved by removing the date/time macros from the source code)

### Question 2 : 
The image from Listing 7 is 291 MB, while the image from Listing 8 is only 8.75 MB. The first image contains the entire Alpine operating system along with the GCC compiler and all build tools (build-base). The multi-stage build (Listing 8) discards the build tools after compilation, keeping only the bare Alpine OS and the compiled executable

### Question 3 : 
The multi-stage container image (8.75 MB) is still significantly larger than the raw compiled binary from Section 2.6.1 (which is only a few kilobytes). This difference exists because the container image must bundle a complete, isolated filesystem and basic operating system environment (Alpine Linux) to ensure the binary can run consistently anywhere

### Question 4 : 
The COPY --from=buildtime-stage instruction extracts the compiled binary from the temporary build stage and places it into the final runtime stage. This is a very good idea because it excludes all the heavy build dependencies from the final image, resulting in a much smaller image, faster deployment, and a reduced security attack surface

### Question 7 : 
A single tar archive (.tar file) is transferred, containing all the immutable filesystem layers and the container's metadata (like the ENTRYPOINT). Yes, this ensures the other student runs the exact same environment, because it bypasses the build process entirely and directly loads the pre-built, isolated filesystem


## Section 4.1.6

### Question 3
Yes. Nix models builds as pure functions, meaning the same inputs always produce the same outputs. It also caches the result in the Nix store

### Question 4
nix shell creates a temporary, isolated environment to execute the package without polluting the host system. nix profile add installs the package permanently in the current user's profile

### Question 5
The Nix store is a directory containing all installed packages and dependencies, identified by unique cryptographic hashes. It is immutable (read-only) to prevent accidental modifications and ensure complete isolation from the host operating system

### Question 6
It generates a flake.lock file that locks all input dependencies to specific revisions. This ensures future builds use the exact same versions across different machines, which is critical for reproducibility

### Question 7
The build would fail. Nix builds are executed in a sandbox with only explicitly declared dependencies available. This restriction prevents hidden or non-deterministic inputs (like network fetches or host files) from affecting the build

### Question 8
The project relies on the flake.lock file, which locks the dependency to a specific, immutable version. The unexpected upstream update will not affect the project until the lockfile is explicitly updated

### Question 9
The flake.nix would use pkgs.mkShell and include pkgs.jdk and pkgs.gcc in the packages list and yes, the flake.lock file must be shared to guarantee everyone gets the exact same versions

## Section 4.2
### 1
Containers reduce runtime variability but do not guarantee build reproducibility due to timestamps or mutating base images. Nix guarantees strict build reproducibility by enforcing pure functions, isolated sandboxes, and immutable content-addressed storage

### 2
No, it is never completely safe. Containers isolate the filesystem but share the host's kernel, meaning kernel vulnerabilities can be exploited. Nix isolates the build process in a sandbox, but running the compiled binary provides no runtime isolation from the host

### 4
I could compile it as a statically linked binary so it includes all its dependencies, or I could package it into a container image and export it as a tarball using podman save
### 5
Yes, learning LaTeX or Typst would be a great skill for writing structured academic report :)
### 6
J'ai apprécié la progression du TP vers Nix, mais l'expérience a été compliquée par des limites techniques de VirtualBox (apres c'est vrai que vous avez mentionné que le tp sera bien de le faire sur linux mais juste les gens qui ont windows et qu'ils vont utilisez VB m). Les ressources système étaient insuffisantes (causant des Segmentation fault), et des problèmes d'affichage graphique ont rendu le terminal instable au début. De plus, le formatage du PDF rend les copier-coller difficiles (fournir les commandes en texte brut éviterait des erreurs de syntaxe).
Si jamais l'erreur de Segmentation fault reste, il faut supprimer nix completement et le re-instaler à nouveau avec la commande dans l'énoncé, par ce que c'est un problème de version qui cause problème. Je vous laisse déja les commandes (à l'aide de GPT) pour résoudre ce problème facilement si jamais avec les futurs étudiants .


### 1. Full System Cleanup
To allow a fresh installation, all residual Nix services, files, and build groups had to be manually removed:

# Stop and disable Nix daemon services
sudo systemctl stop nix-daemon.service nix-daemon.socket

sudo systemctl disable nix-daemon.service nix-daemon.socket

# Remove all Nix directories and configuration files
sudo rm -rf /nix /etc/nix ~/.nix-profile ~/.nix-defexpr ~/.nix-channels

# Delete residual build users and the nixbld group
for i in $(seq 1 32); do sudo userdel nixbld$i 2>/dev/null; done

sudo groupdel nixbld 2>/dev/null

### 2. Clean Installation : The installation was performed using the official script provided in the protocol:

curl -sSfL https://artifacts.nixos.org/nix-installer | sh -s install

### 3. Environment Activation : After installation, the shell environment was refreshed to recognize the nix command

. /nix/var/nix/profiles/default/etc/profile.d/nix-daemon.sh
