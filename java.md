#### Connexió a una SGBD MySQL
Codi molt simple per veure les funcions bàsiques de connexió a un SGBD MySQL i  realitzar les operacioins d'INSERT i SELECT 

Els exemples estan contextualitzats a la BD rrhh.

Podeu trobar més informació:
- Instal·lació: https://dev.mysql.com/doc/connector-python/en/connector-python-installation.html
- Exemples: https://dev.mysql.com/doc/connector-python/en/connector-python-examples.html

**INSERT**
```python

import mysql.connector
import datetime

cnx = mysql.connector.connect(host='127.0.0.1',user='usuari',password='paraulapas', database='rrhh')
cursor = cnx.cursor()

avui = datetime.datetime.now().date()
#dema = datetime.now().date() + timedelta(days=1)
#date(1977, 6, 14)

stm_insert_empleat = ("INSERT INTO empleats "
            "(empleat_id, nom,cognoms,email,data_contractacio,feina_codi,salari) "
            "VALUES (%s, %s, %s, %s, %s, %s, %s)")
dades_empleat = (500,'Geert', 'Vanderkelen','gvanderkelen@sapalomera.cat', tomorrow, 'IT_PROG',77.99)

# Executem l'INSERT
cursor.execute(insert_empleat, data_employee)
# Si la taula tenia un valor autoincremental aquest es pot recollir mitjançant lastrowid o _last_insert_id.
empleat_id = cursor.lastrowid    
print(cursor._last_insert_id)

# Ens assegurem de realitzar un commit a la BD
cnx.commit()
#allibarem recursos
cursor.close()
cnx.close()
```

**SELECT/QUERY**
```python

import mysql.connector
import datetime

cnx = mysql.connector.connect(host='127.0.0.1',user='usuari',password='paraulapas', database='rrhh')
cursor = cnx.cursor()

query = ("SELECT empleat_id,nom,cognoms,data_contractacio 
         "   FROM empleats "
         "WHERE data_contractacio BETWEEN %s AND %s")

inici = datetime.date(1990, 1, 1)
fi = datetime.date(2016, 12, 31)

cursor.execute(query, (inici, fi))

for (e_id, e_nom, e_cognoms, e_data_contractacio) in cursor:
    print("{} - {}, {} va ser contractat el {:%d %b %Y}".format(e_id,e_nom,e_cognoms,e_data_contractacio))

cursor.close()
cnx.close()
    
```
#### Descomprimir un fitxer comprimit .zip
Exemple de lectura d'un fixer pla per només lectura.
```python
import zipfile
import os

dirActual = os.path.dirname(__file__)
print (dirActual)
nomFitxerZip = "prova.zip"
dirUnzip = os.path.join(os.path.dirname(__file__) , "unzip")
pathFitxerZip = os.path.join(dirActual,nomFitxerZip)
print (dirUnzip)

# Extraiem el contingut de pathFitxerZip a dirUnzip
with zipfile.ZipFile(pathFitxerZip, 'r') as zipRef:
zipRef.extractall(dirUnzip)
```

#### Llegir un fitxer pla
Exemple de lectura d'un fixer pla per només lectura.
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import static java.nio.charset.StandardCharsets.UTF_8;
import static java.nio.charset.StandardCharsets.ISO_8859_1;
import java.nio.file.*;

public static void main(String[] args) {
        BufferedReader bfLector = null;
        try {
         
            //Obtenim el directori actual
            Path pathActual = Paths.get(System.getProperty("user.dir"));

            //Concatenem el directori actual amb un subdirectori "dades" i afegim el fitxer "03021911.DAT"
            Path pathFitxer = Paths.get(pathActual.toString(), "dades", "03021911.DAT");

            //objReader = new BufferedReader(new FileReader(pathFitxer.toString()));

            bfLector = Files.newBufferedReader(pathFitxer, StandardCharsets.ISO_8859_1);
            String strLinia;
            while ((strLinia = bfLector.readLine()) != null) {
                System.out.println(strLinia);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (bfLector != null)
                    bfLector.close();
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        }
    }
```

#### Llegir un fitxer .xls
Exemple de lectura d'un fixer XLS. l'exemple està basat en el fitxer que es troba dins d'algun dels fitxers .zip de `<Resultados Eleccions Generales - 02_199306_1.zip>` : <https://github.com/robertventura/databases/tree/master/db_eleccions_generals/data/resultats_x_municipi>

Cal afegir les llibreries de la carpeta libs/poi o descarregar-les del web oficial.

Podeu trobar més informació a:
- https://poi.apache.org/
- https://mkyong.com/java/apache-poi-reading-and-writing-excel-file-in-java/ 

```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Iterator;

try {

	//Obtenim el directori actual
	Path pathActual = Paths.get(System.getProperty("user.dir"));

	//Concatenem el directori actual amb un subdirectori "dades" i afegim el fitxer "03021911.DAT"
	String nomFitxer = "02_201904_1.xlsx";
  
	Path pathFitxer = Paths.get(pathActual.toString(), "dades",nomFitxer );
	
	FileInputStream excelFile = new FileInputStream(new File(pathFitxer.toString()));
	Workbook workbook = new XSSFWorkbook(excelFile);
	Sheet fulla = workbook.getSheetAt(0); // Obtenim la primera fulla
	
	/*
	fila 7 comencen les dades
	Columnes:
	   1: Nom de la comunitat
	   2: Codi de Provincia
	   3: Nom de la Provincia
	   4: Codi de Municipi
	   5: Nom de Municipi
	   6: Població
	   7: Número de meses
	   8: Total del cens electoral
	   9: Total de vots
	   10: Vots vàlids
	   11: Vots a candidatures
	   12: Vots en blanc
	   13: Vots nuls
	   14: shee.max_column (partits polítics)
	*/
	
	//fulla.getLastRowNum() retornem índex de la última fila
	//fila.getLastCellNum() retornem índex de la última cela dins de la fila.
	
	//Recorrem  97 files de la fulla            
	for(int i = 7; i<100; i++) {
		Row fila = fulla.getRow(i);
		//Imprimim els valors de la columna 4 i 5
		Cell cela = fila.getCell(3);
		
		System.out.print(cela.getNumericCellValue() + "--");
		
		System.out.println(fila.getCell(4).getStringCellValue() + "--");                
	}
	
	/*
	-- Construïm un interador sobre la fulla.            
	*/
	Iterator<Row> rowIterator = fulla.iterator();
	
	 // Bucle per recòrrer totes les files i columnes de la fulla
	while(rowIterator.hasNext()) {
		
		Row filaActual  = rowIterator.next();
		
		//Construïm un interador per les columnes.
		Iterator<Cell> cellIterator = filaActual.iterator();                
		while (cellIterator.hasNext()) {
			
			Cell celaActual = cellIterator.next();
			
			if (celaActual.getCellType() == CellType.STRING) {
				System.out.print(celaActual.getStringCellValue() + "--");
			} else if (celaActual.getCellType() == CellType.NUMERIC) {
				System.out.print(celaActual.getNumericCellValue() + "--");
			}

		}
		System.out.println();
	}

} catch (FileNotFoundException e) {
	e.printStackTrace();
} catch (IOException e) {
	e.printStackTrace();
}
```
