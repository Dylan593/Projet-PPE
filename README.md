# Projet-PPE

function commander(){
		//L'appel de cette commande se fait seulement si des blagues ont déjà été commander donc pas besoin
		// de faire une verification dessus
        $test="(";
		foreach($_SESSION['panier'] as $ligne => $value){
			$test.=$ligne.",";
		}
		$test.="0)";
		$blague = Model::load("blagues");
		$blagueRes = array("produit"=>$blague->find(Model::connexion(), array('condition'=>"id_blague in $test")));
		$variables['cmde']=array('mescommandes'=>$blagueRes['produit']);
		$this->set($variables);
		Model::deconnexion();
		$today = date("Y-m-d");
		$idA = $_SESSION['auteur'][0]->id_auteur;
		$total = 0;
		foreach($blagueRes['produit'] as $ligne => $prop){
			$total = $total + $prop -> px_blague;
		}
		$data= array(
			"id_auteur"=>$idA,
			"dated_commande"=>$today,
			"tot_commande"=>$total
		);
		$tabCom = Model::load("commande");
		$tabCom -> save(Model::connexion(),$data);
		
		$lcommandeRes = array("produit"=>$tabCom->find(Model::connexion(), array('condition'=>"id_auteur = $idA && dated_commande = '$today' && tot_commande = $total")));
		Model::deconnexion();
		
		$tabLCom = Model::load("lcommande");
		foreach($blagueRes['produit'] as $ligne => $prop) {
			$data2 = array(
				"id_commande"=>$lcommandeRes['produit'][0]->id_commande,
				"id_blague"=>$prop -> id_blague
			);
			$tabLCom -> save(Model::connexion(),$data2);
		}
		Model::deconnexion();
		$_SESSION['panier'] = null;
		
        $this->render('commander');
    }
    
    ____________________________________________________________________________________________________________________________________
    
    function commanderfinaliser(){
		
       $this->render('commanderfinaliser');
        
    }
    
    ____________________________________________________________________________________________________________________________________
    
    <section class="page-section" id="commanderfinaliser">
    <div class="container">
      <div class="row">
        <div class="col-lg-12 text-center">
			<h1>Merci de nous avoir accordé votre confiance !</h1>
			<br> <br>
			<h5> Votre paiement a bien été effectué ! </h5>

<a href="<?= WEBROOT.'blague' ?>">
	<button type="button" style="margin:20px" >
		POURSUIVRE 
	</button>
</a>
</div>
</div>
</div>
</section>
