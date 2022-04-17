import ilog.concert.*;
import ilog.cplex.*;
public class Problème10 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		 calcul();
	}
		 public static void calcul (){
			 try {
				 IloCplex simplexe = new IloCplex ();
				 
				 int nville=6;
				 int nhub=2; 
				 int Distance_ij[][]= {{0,945,605,4667,4749,4394},
				  							 {945,0,866,3726,3806,3448},
				  							 {605,866,0,4471,4541,4152},
				  							 {4667,3721,4471,0,109,415},
				  							 {4749,3806,4541,109,0,431},
				  							 {4394,3448,4152,415,431,0}};
				  							 
				  int Quantite_ij[][]= {{0,500,1000,300,400,1500},
				  							 {1500,0,250,630,360,1140},
				  							 {400,510,0,460,320,490},
				  							 {300,600,810,0,820,310},
				  							 {400,100,420,730,0,970},
				  							 {350,1020,260,580,380,0}};	

				  double cout_ijkm [][][][] = null;
				  
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville;j++) {
						  for(int k=0 ; k < nville; k++) {
							  for(int m=0 ; m < nville; m++) {
								  cout_ijkm[i][j][k][m]=Distance_ij[i][k]+(Distance_ij[k][m]*0.8)+Distance_ij[m][j];
								  
							  }}}};
				
							 
				  IloNumVar[][][][] flow_ijkm = new IloNumVar[nville][nville][nville][];
				 // IloNumVar[] hub = new IloNumVar[nville];
				//  hub= simplexe.boolVarArray(nville);

				  IloNumVar[] hub = new IloNumVar[nville]; // déclaration des Variables de décision de type boolean
					for (int i=0;i<nville;i++){
						hub[nville]= simplexe.boolVar();
					}
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville;j++) {
						  for(int k=0 ; k < nville; k++) {		
							  flow_ijkm[i][j][k] = simplexe.boolVarArray(nville);
						  }}}
				// declaration de la fonction objectif
				  IloLinearNumExpr objectif = simplexe.linearNumExpr();
				 // Définition des coefficients de la fonction objectif
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville;j++) {
						  for(int k=0 ; k < nville; k++) {
							  for(int m=0 ; m < nville; m++) {
								 objectif.addTerm(flow_ijkm[i][j][k][m],Quantite_ij[i][j]*cout_ijkm[i][j][k][m]);
							  }}}};
				 // Définir le type d'otimisation de la fonction ( min )
				  simplexe.addMinimize(objectif);

				// contraint 1
				  simplexe.addEq(simplexe.sum(hub),2);
							
				// contraint 2
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville; j++) {
						  for(int k=0 ; k < nville; k++) {
							  for(int m=0 ; m < nville; m++) {
						 simplexe.addEq(simplexe.sum((IloIntSetVar) flow_ijkm[i][j][k][m]),1);
							  }} }}

				  
				  // contraint 3
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville; j++) {
						  for(int k=0 ; k < nville; k++) {
							  for(int m=0 ; m < nville; m++) {
						  IloLinearNumExpr c1 = simplexe.linearNumExpr();
						  IloLinearNumExpr c2 = simplexe.linearNumExpr();

						c1.addTerm(1, hub[k]);
						c2.addTerm(1, flow_ijkm[i][j][k][m]);
						simplexe.addLe(c1, c2);
				  }}}}
				
				// contraint 4	  
				  for(int i=0 ; i < nville; i++) {
					  for(int j=0 ; j < nville; j++) {
						  for(int k=0 ; k < nville; k++) {
							  for(int m=0 ; m < nville; m++) {
										  IloLinearNumExpr c1 = simplexe.linearNumExpr();
										  IloLinearNumExpr c2 = simplexe.linearNumExpr();

										c1.addTerm(1, hub[m]);
										c2.addTerm(1, flow_ijkm[i][j][k][m]);
										simplexe.addLe(c1, c2);
								  }}}}


				  
				  
				 
				  simplexe.solve(); // lancer la resolution
				// Afficher des résultat
				} catch (IloException e){
				System.out.print("Exception levée " + e);
				 }
				}
				}
