# CESI-Strong-box-3000
Code finale strongbox 3000 sur arduino ( C++) fichier en .ino

Code à ouvrir avec le logiciel arduino: 
Download --> https://www.arduino.cc/en/software 

Ce code ne prend pas en compte de la tension de sortie de votre circuit éléctrique qui vous à normalement était demandés. 
Ce code récupère de base 5 Volt en tension Ve établie par la tension d'entrée du schéma éléctrique. 
Pour finir ce code est la traduction de votre prototype fait en logigramme à l'aide du logiciel flowgorithm (ce logiciel vous permet de faire un pseudo-code pour structurer le code finale coder en C++ sur l'arduino). Attention lors de votre prototype flowgorithm vous allez rencontrer un problème, celui de ne pas pouvoir créer une liste ( tableau ) avec plusieurs paramètres. 
Il faut juste savoir que si ca marche avec 2 noms d'agent ca marche avec 16 

//On considère que l'Aruduino a capté la tension de sortie du circuit interne du coffre.

char nomAgent;
char InfosNomsAgents[16] = {'A','B','C','D','E','F','H','I','J','K','L','M','N','O','P','Q'};
int message;
int messageChiffre;
const int n = 43*67;
int clePublique;

const int DataPin = 8;
const int IRQpin =  5;

void bonjour(char agent){
  Serial.print("Bonjour agent ");
  Serial.print(agent);
  Serial.println(", veuillez finaliser votre authentification.");
}

void felicitations(char agent){
  Serial.print("\n\nFélicitations agent ");
  Serial.print(agent);
  Serial.println(", vous avez été authentifié avec succès.");
}

int Chiffrer(int msg, int clePub){
  int messageChiffre = Modexp(msg, clePub, n);
  return messageChiffre;
}

int Modexp(int modexMsg, int modexClePub, int modexN){
  long r;
  if (modexMsg < 0) {
    Serial.print("unhandled case");
    return -1;
  }
  if ((modexMsg == 0)||(modexN == 1)){
    return 0;
  }
  r = 1;
  do {  
    r = (r*(modexMsg%modexN))%modexN;
    modexClePub--;
  } while (modexClePub > 0);
  return r;
}

int CleAgent(char agent){
  int InfosClesPubliques[16]={601,619,631,641,647,653,661,673,691,701,733,739,751,797,809,811};
  clePublique=-9999;
  for (int i=0; i<16; i++){
    if (agent == InfosNomsAgents[i]){
      clePublique = InfosClesPubliques[i];
      break;
    }
  }
  return clePublique;
}

boolean NS1(char agent){
  if (MA1()){
    Serial.println("Etape complétée avec succès, veuillez continuer votre authentification");
    if (MA3(agent)){
      return true;
    } 
    else {
      return false;
    }
  } 
}

boolean NS2(char agent){
  if (MA1()){
    Serial.println("\n\nEtape complétée avec succès, veuillez continuer votre authentification");
    return MA4(agent);
  }
  return false;
}

boolean NS3(char agent){
  if (MA2(agent)) {
    Serial.println("\n\nEtape complétée avec succès, veuillez continuer votre authentification");
    return MA5(agent);
  }
  return false;
}

boolean NS4(char agent){
  if (MA2(agent)) {
    Serial.println("n\nEtape complétée avec succès, veuillez continuer votre authentification");
    if (MA3(agent)) {
      Serial.println("n\nEtape complétée avec succès, veuillez continuer votre authentification");
      return MA4(agent);
    }
  }
  return false;
}

boolean NS5(char agent){
  if (MA1()) {
    Serial.println("n\nEtape complétée avec succès, veuillez continuer votre authentification");
    if (MA2(agent)) {
      Serial.println("n\nEtape complétée avec succès, veuillez continuer votre authentification");
      if (MA3(agent)) {
         Serial.println("n\nEtape complétée avec succès, veuillez continuer votre authentification");
         return (MA5(agent));
      }
    }
  }
  return false;
}

boolean MA1(){
  Serial.println("Question 1 :");
  Serial.println("Quel est l'animal qui marche le matin sur quatre pattes, à midi sur deux pattes et le soir sur trois pattes ?");
  Serial.println("1 - Le chien");
  Serial.println("2 - Le cheval");
  Serial.println("3 - L'Homme");
  Serial.println("4 - Le mille-pattes");
  do {
  } while(Serial.available()==0);
  int reponse=Serial.parseInt();
  if (reponse == 3){
    Serial.println("Bonne réponse.");
    Serial.println("Question 2 :");
    Serial.println("Dans la mythologie nordique, qui est le frère de Thor ?");
    Serial.println("1 - Loki");
    Serial.println("2 - Sigurd");
    Serial.println("3 - Odin");
    Serial.println("4 - Hulk");
    do {
    } while(Serial.available()==0);
    reponse=Serial.parseInt();
    if (reponse == 1){
      Serial.println("Bonne réponse.");
      Serial.println("Question 3 :");
      Serial.println("Quelle créature n'apparaît pas dans la mythologie grecque ?");
      Serial.println("1 - Le Centaure");
      Serial.println("2 - L'Hydre");
      Serial.println("3 - Cerbère");
      Serial.println("4 - Fenrir");
      do {
      } while(Serial.available()==0);
      reponse=Serial.parseInt();
      if (reponse == 4){
        Serial.println("Bonne réponse.");
        return true;
      }
    }
  }
  Serial.println("Erreur. ALERTE INTRUSION");
  return false;
}

boolean MA2(char agent){
  int messageDechiffre;
  message = random(2881);
  clePublique = CleAgent(agent);
  messageChiffre = Chiffrer(message, clePublique);
  Serial.print("\n\nDéchiffrez ce message :");
  Serial.println(messageChiffre);
  do {
  } while(Serial.available()==0);
  messageDechiffre = Serial.parseInt();
  if (messageDechiffre == message) {
    return true;
  }
  Serial.println("Erreur. ALERTE INTRUSION");
  return false;
}

boolean MA3(char agent){
  String InfosCouleurYeux[16]={"Vert clair","Vert foncé","Bleu clair","","","","","","","","","","","Bleu foncé","Marron clair","Marron foncé"};
  Serial.println("\n\nPlacez votre oeil devant le scanner");
  do {
  } while(Serial.available()==0);
  String CouleurOeil = Serial.readStringUntil('\n');
  for (int i=0; i<16; i++){
    if (agent == InfosNomsAgents[i]){
      if (CouleurOeil == InfosCouleurYeux[i]){
        return true;
      }  
    }
  }
  Serial.println("Erreur, ALERTE INTRUSION");
  return false;
}

boolean MA4(char agent){
  String InfosEmpreintesDigitales[16]={"","","","Pouce droit","Index droit","Majeur gauche","Annulaire droit","Auriculaire gauche","","","","","","Pouce gauche","Index gauche",""};
  Serial.print("\n\nPlacez votre doigt sur le scanner");
  do {
  } while(Serial.available()==0);
  String EmpreinteDigitale = Serial.readStringUntil('\n');
  for (int i=0; i<16; i++){
    if (agent == InfosNomsAgents[i]){
      if (EmpreinteDigitale == InfosEmpreintesDigitales[i]){
        return true;
      }  
    }
  }
  Serial.println("\n\nErreur, ALERTE INTRUSION");
  return false;
}

boolean MA5(char agent){
  String InfosIDCartes[16]={"","","","","","","","","0001","0010","0011","0100","0101","","","0111"};
  Serial.print("\n\nEntrez votre ID Carte");
  do {
  } while(Serial.available()==0);
  String IDCarte = Serial.readStringUntil('\n');
  for (int i=0; i<16; i++){
    if (agent == InfosNomsAgents[i]){
      if (IDCarte == InfosIDCartes[i]){
        return true;
        }  
    }
  }
  Serial.println("\n\nErreur, ALERTE INTRUSION");
  return false;
}

void setup() {
  Serial.begin(9600); // démarre une connexion en série à 9600 bits/s
  Serial.setTimeout(50);
  Serial.println("Strongbox 3000");
  Serial.println("Entrez votre nom d'agent : ");
  randomSeed(analogRead(0));
}

void loop() {
  if (Serial.available()) {
    nomAgent = Serial.read();
    switch (nomAgent) {
      case 'A'...'C':
        //if ((nomAgent == 'A') || (nomAgent == 'B') || (nomAgent == 'C')){
        bonjour(nomAgent);
        if (NS1(nomAgent)){
          felicitations(nomAgent);
        }
        break;
      case 'D'...'F':
      case 'H'...'I':
        bonjour(nomAgent);
        if (NS2(nomAgent)){
          felicitations(nomAgent);
        }
        break;
      case 'J'...'N':
        bonjour(nomAgent);
        if (NS3(nomAgent)){
          felicitations(nomAgent);
        }
        break;
      case 'O'...'P':
        bonjour(nomAgent);
        if (NS4(nomAgent)){
          felicitations(nomAgent);
        }
        break;
      case 'Q':
        bonjour(nomAgent);
        if (NS5(nomAgent)){
          felicitations(nomAgent);
        }
        break;
      default:
        Serial.println("Aucun agent ne porte ce nom. ALERTE INTRUSION");
        break;
    }
  }
}
code_coffre_fort.ino
8 Ko
