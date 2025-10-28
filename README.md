# Simulador-de-rpg.py
import random
from abc import ABC, abstractmethod

# Classe base para personagens
class Personagem(ABC):
    def __init__ (self, nome, vida, mana, forca):
        self.nome = nome
        self.vida = vida
        self.mana = mana
        self.forca = forca

    def esta_vivo(self):
        """Verifica se o personagem está vivo."""
        return self.vida > 0
    
    def receber_dano(self, dano):
        """Reduz a vida do personagem ao receber dano."""
        self.vida -= dano
        if self.vida < 0:
            self.vida = 0
        return f"{self.nome} recebeu {dano} de dano! Vida atual: {self.vida}"
        
    @abstractmethod
    def atacar(self, inimigo):
        """Método abstrato para atacar um inimigo."""
        pass

    # Subclasses Heróis
class Guerreiro(Personagem):
    def __init__(self, nome):
        super().__init__(nome, vida=260, mana=50, forca=40)

    def atacar(self, inimigo):
        dano = self.forca + random.randint(5, 15)
        inimigo.receber_dano(dano)
        return f"{self.nome} ataca {inimigo.nome} causando {dano} de dano!"
    
    def defender(self):
        defesa = random.randint(10, 30)
        self.vida += defesa
        return f"{self.nome} se defende e recupera {defesa} de vida!"
    
class Mago(Personagem):
    def __init__(self, nome):
        super().__init__(nome, vida=120, mana=200, forca=60)

    def atacar(self, inimigo):
        if self.mana >= 20:
            dano = self.forca + random.randint(20, 40)
            inimigo.receber_dano(dano)
            self.mana -= 20
            return f"{self.nome} lança um feitiço em {inimigo.nome} causando {dano} de dano!"
        else:
            return f"{self.nome} não tem mana suficiente para atacar!"
    
    def recuperar_mana(self):
        recuperacao = random.randint(15, 30)
        self.mana += recuperacao
        return f"{self.nome} recupera {recuperacao} de mana!"
    
class Arqueiro(Personagem):
    def __init__(self, nome):
        super().__init__(nome, vida=150, mana=100, forca=50)

    def atacar(self, inimigo):
        dano = self.forca + random.randint(10, 25)
        inimigo.receber_dano(dano)
        return f"{self.nome} dispara uma flecha em {inimigo.nome} causando {dano} de dano!"
    
    def esquivar(self):
        esquiva = random.randint(5, 20)
        self.vida += esquiva
        return f"{self.nome} se esquiva e recupera {esquiva} de vida!"
import random
from personagens import Personagem

# Classe base para inimigos

class Goblin(Personagem):
    def __init__(self):
        super().__init__(nome="Goblin", vida=60, mana=30, forca=15)

    def atacar(self, inimigo):
        dano = self.forca + random.randint(5, 10)
        inimigo.receber_dano(dano)
        return f"{self.nome} ataca {inimigo.nome} causando {dano} de dano!"
    
class Orc(Personagem):
    def __init__(self):
        super().__init__(nome="Orc", vida=110, mana=20, forca=30)

    def atacar(self, inimigo):
        dano = self.forca + random.randint(10, 20)
        inimigo.receber_dano(dano)
        return f"{self.nome} ataca {inimigo.nome} causando {dano} de dano!"
    
class Dragao(Personagem):
    def __init__(self):
        super().__init__(nome="Dragão", vida=300, mana=100, forca=40)

    def atacar(self, inimigo):
        dano = self.forca + random.randint(20, 40)
        inimigo.receber_dano(dano)
        return f"{self.nome} cospe fogo em {inimigo.nome} causando {dano} de dano!"
    
    def rugir(self):
        """Dragão solta um rugido aterrorizante, ganha mais força temporariamente."""
        ganho = random.randint(10, 25)
        self.forca += ganho
        return f"{self.nome} solta um rugido aterrorizante! Força aumentada em {ganho}!"

        import random
import time
from personagens import Guerreiro, Mago, Arqueiro
from inimigos import Goblin, Orc, Dragao


# =============================
# SISTEMA DE BATALHA
# =============================

class Batalha:
    def __init__(self, herois, inimigos):
        self.herois = herois
        self.inimigos = inimigos
        self.turno = 1

    def mostrar_status(self):
        print("\n=== STATUS ATUAL ===")
        for h in self.herois:
            print(f"🛡️ {h.nome} - HP: {h.vida} | Mana: {h.mana}")
        for i in self.inimigos:
            print(f"👹 {i.nome} - HP: {i.vida}")
        print("=" * 30)

    def rodada(self):
        print(f"\n⚔️ ROUND {self.turno}")
        time.sleep(1)

        # Turno dos heróis
        for heroi in self.herois:
            if heroi.esta_vivo() and self.inimigos:
                inimigo = random.choice(self.inimigos)
                acao = random.choice(["atacar", "especial"])

                if acao == "atacar":
                    print(heroi.atacar(inimigo))
                else:
                    # ações especiais
                    if isinstance(heroi, Guerreiro):
                        print(heroi.defender())
                    elif isinstance(heroi, Mago):
                        print(heroi.recuperar_mana())
                    elif isinstance(heroi, Arqueiro):
                        print(heroi.esquivar())

                if not inimigo.esta_vivo():
                    print(f"💀 {inimigo.nome} foi derrotado!")
                    self.inimigos.remove(inimigo)

                time.sleep(1)

        # Turno dos inimigos
        for inimigo in self.inimigos:
            if inimigo.esta_vivo() and self.herois:
                heroi = random.choice(self.herois)
                print(inimigo.atacar(heroi))
                if not heroi.esta_vivo():
                    print(f"☠️ {heroi.nome} caiu em batalha!")
                    self.herois.remove(heroi)
                time.sleep(1)

        self.turno += 1

    def iniciar(self):
        print("🔥 A BATALHA COMEÇA! 🔥")
        time.sleep(1)

        while self.herois and self.inimigos:
            self.mostrar_status()
            self.rodada()

        if self.herois:
            print("\n🎉 Vitória dos Heróis!")
        else:
            print("\n💀 Os inimigos triunfaram...")
# Exemplo de uso

if __name__ == "__main__":
    # Cria heróis
    herois = [Guerreiro("Arthur"), Mago("Merlin"), Arqueiro("Legolas")]

    # Cria inimigos
    inimigos = [Goblin(), Orc(), Dragao()]

    # Inicia batalha
    jogo = Batalha(herois, inimigos)
    jogo.iniciar()


        
        
