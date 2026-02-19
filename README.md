# 3G - Grace Hopper and Game Lab for Girls 2026

# Aula 1

## O que é Phaser?

## Criando a Base do Jogo

- Criar uma pasta e os arquivos necessários.
    - Criar pasta assets;
    - Criar pasta scenes;
    - Criar arquivo index.html;
    - Criar arquivo main.js;
    - Criar arquivo game.js na pasta scenes;

- Linkar o Phaser no HTML.
    - Adicionar a versão minificada do phaser:
    ```
    <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script> 
    ```

- Criar configurações do Phaser
    ```
    import { GameScene } from "./scenes/game.js";

    const config = {
        type: Phaser.AUTO,
        width: 800,
        height: 600,
        backgroundColor: "#c1a0e0",
        pixelArt: true,
        roundPixel: false,
        scale: {
            mode: Phaser.Scale.FIT,
            autoCenter: Phaser.Scale.CENTER_BOTH
        },
        physics: {
            default: "arcade",
            arcade: {
                gravity: { y: 400 },
                debug: true
            }
        },
        scene: [GameScene]
    };

    const game = new Phaser.Game(config);
    ```
- Criar a estrutura de uma cena.
    ``` 
    export class GameScene extends Phaser.Scene {

        constructor() {

        }
        preload() {

        }
        create() {

        }
        update() {

        }
    }
    ```
    
## Adicionando elementos visuais:
- Criar um cenário.
     ```
     export class GameScene extends Phaser.Scene {

    alturaJogo = 600;
    larguraJogo = 800;

    constructor() {
        super("MainScene");
    }

    preload() {
        this.load.image("paisagem", "../assets/paisagem.png");
    }

    create() {
        this.add.image(this.larguraJogo/2, this.alturaJogo/2, "paisagem").setScale(0.6);
    }
     ```
- Inserir um personagem.
    ```
    
    preload() {
        this.load.spritesheet("grace_sprite", "../assets/spritesheetGrace.png", { frameWidth: 64, frameHeight: 64 })
    }

    create() {
        this.add.image(this.larguraJogo/2, this.alturaJogo/2, "paisagem").setScale(0.6);

        this.player = this.physics.add.sprite(this.larguraJogo/2, 100, 'grace_sprite').setScale(1.3);
        this.player.setCollideWorldBounds(true);
    }
    ```
- Criar plataformas.
    ```
    preload() {
        this.load.image("plataforma", "../assets/plataforma.png");
    }

    create() {
        this.plataformas[0] = this.physics.add.staticImage(200, 450, 'plataforma');
        this.plataformas[0].body.setSize(148, 44, true);
        this.plataformas[0].setScale(0.3);

        this.plataformas[1] = this.physics.add.staticImage(580, 360, 'plataforma');
        this.plataformas[1].body.setSize(148, 44, true);
        this.plataformas[1].setScale(0.3);
    }
    ```
## Introdução à física no Phaser:
- Aplicação de gravidade no config
    ```
    physics: {
        default: "arcade",
        arcade: {
            gravity: { y: 400 },
            debug: true
        }
    }
    ```
- Configuração de colisões entre personagem e plataformas.
    ```
    create() {
        for (let i = 0; i < this.plataformas.length; i++){
            this.physics.add.collider(this.player, this.plataformas[i]);
        }
    }
    ```

## Implementação de Movimentação e Controles

- Capturar entrada do usuário.
    ```
    create() {
        this.cursors = this.input.keyboard.createCursorKeys();
    }
    ```
- Aplicar movimentação ao personagem.
    ```
    update() {
        // Frente e trás (horizontal)

        if (this.cursors.left.isDown) {
            this.player.setVelocityX(-160);
        } else if (this.cursors.right.isDown) {
            this.player.setVelocityX(160);
        } else {
            this.player.setVelocityX(0);
        }

        // Pulo (vertical) 

        if (this.cursors.up.isDown)
            this.player.setVelocityY(-400);
        }
        if (this.cursors.down.isDown) {
            this.player.setVelocityY(400);
        }
    }
    ```
- Ajustando animações ao movimento.
    ```
    create() {
        // Animações da personagem
        this.anims.create({
            key: 'direita',
            frames: this.anims.generateFrameNumbers('grace_sprite', { start: 5, end: 8 }),
            frameRate: 10,
            repeat: -1
        });

        this.anims.create({
            key: 'esquerda',
            frames: this.anims.generateFrameNumbers('grace_sprite', { start: 0, end: 3 }),
            frameRate: 10,
            repeat: -1
        });

        this.anims.create({
            key: 'parada',
            frames: [{ key: 'grace_sprite', frame: 4 }],
            frameRate: 20
        });
    }
    ```

    ```
    update() {
        if (this.cursors.left.isDown) {
            this.player.setVelocityX(-160);
            if (this.player.anims.currentAnim?.key !== 'esquerda') {
                this.player.anims.play('esquerda', true);
            }
        } else if (this.cursors.right.isDown) {
            this.player.setVelocityX(160);
            if (this.player.anims.currentAnim?.key !== 'direita') {
                this.player.anims.play('direita', true);
            }
        } else {
            this.player.setVelocityX(0);
            if (this.player.anims.currentAnim?.key !== 'parada') {
                this.player.anims.play('parada', true);
            }
        }
    }
    ```

## Criando Estrutura de Cenas

- Introdução ao conceito de **Scenes** no Phaser.
- Criar uma cena de tela inicial e uma cena de fase jogável.
    - Criar o arquivo welcome.js na pasta scenes
    ```
    export class WelcomeScene extends Phaser.Scene {

    alturaJogo = 600;
    larguraJogo = 800;

    constructor() {
        super("WelcomeScene");
    }

    preload() {
        this.load.image("paisagem", "../assets/paisagem.png");
        this.load.image("computador", "../assets/computador_paisagem.png");
        this.load.image("grace", "../assets/grace.png");
        this.load.image("descricao", "../assets/decricao.png");
        this.load.image("titulo", "../assets/grace_quest.png");
        this.load.image("play", "../assets/botao_play.png");
    }

    create() {
        this.add.image(this.larguraJogo/2, this.alturaJogo/2, "computador");
        this.add.image(this.larguraJogo/2, 130, "titulo").setScale(0.25);
        this.add.image(this.larguraJogo/2, 205, "grace").setScale(0.15);
        this.add.image(this.larguraJogo/2, 350, "descricao").setScale(0.4);
        this.botaoJogar = this.add.image(this.larguraJogo/2, 290, "play").setScale(0.2).setInteractive();

        this.botaoJogar.on("pointerover", () => {
            this.input.setDefaultCursor("pointer");
        });
        
        this.botaoJogar.on("pointerout", () => {
            this.input.setDefaultCursor("default");
        });
    }

    update() {

    }
    }
    ```
- Adicionar a lógica de transição entre cenas.
    - Adicionar ao fim do create() na WelcomeScene
    ```
    this.botaoJogar.on("pointerdown", () => {
            this.scene.start("MainScene")
        })
    ```
    - Adicionar a cena no main.js
    ```
    import { WelcomeScene } from "./scenes/welcome.js";

    scene: [WelcomeScene, GameScene]
    ```

# Aula 2


