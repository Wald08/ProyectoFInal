#include <iostream>
#include <map>
#include <vector>
#include <string>

using namespace std;

/*Estructura para almacenar los datos del usuario*/
struct Usuario {
    string nombre;
    float altura;
    float peso;
    string direccion;
    string telefono;
    string correo;
    int edad;
    string genero;
    string actividadFisica;
};

/* Función para ingresar los datos personales del usuario*/
void ingresarDatosPersonales(Usuario& usuario) {
    cout << "Ingrese su nombre completo: ";
    getline(cin, usuario.nombre);
    cout << "Ingrese su altura (en cm): ";
    cin >> usuario.altura;
    cout << "Ingrese su peso (en kg): ";
    cin >> usuario.peso;
    cout << "Ingrese su edad: ";
    cin >> usuario.edad;
    cin.ignore();  // Limpiar buffer de entrada
    cout << "Ingrese su genero (Masculino/Femenino): ";
    getline(cin, usuario.genero);
    cout << "Ingrese su direccion: ";
    getline(cin, usuario.direccion);
    cout << "Ingrese su telefono: ";
    getline(cin, usuario.telefono);
    cout << "Ingrese su correo: ";
    getline(cin, usuario.correo);
    cout << "Ingrese su nivel de actividad fisica (sedentario/moderado/alto): ";
    getline(cin, usuario.actividadFisica);
}

/*Función para mostrar los departamentos y seleccionar uno*/
string seleccionarDepartamento(const map<string, vector<string>>& departamentos) {
    int contador = 1;
    map<string, vector<string>>::const_iterator it;
    cout << "\nSeleccione un Departamento:\n";
    for (it = departamentos.begin(); it != departamentos.end(); ++it) {
        cout << contador << ". " << it->first << endl;
        contador++;
    }

    int seleccion;
    cout << "Ingrese el número del departamento que desea seleccionar: ";
    cin >> seleccion;
    cin.ignore();  /* Limpiar buffer de entrada*/

    /* Obtener el nombre del departamento seleccionado*/
    auto departamentoSeleccionado = departamentos.begin();
    advance(departamentoSeleccionado, seleccion - 1);

    return departamentoSeleccionado->first;  /* Retorna el nombre del departamento seleccionado*/
}

/* Función para mostrar los municipios del departamento seleccionado y elegir uno*/
string seleccionarMunicipio(const map<string, vector<string>>& departamentos, const string& departamento) {
    auto it = departamentos.find(departamento);
    if (it != departamentos.end()) {
        int contador = 1;
        cout << "\nSeleccione un Municipio de " << departamento << ":\n";
        for (const string& municipio : it->second) {
            cout << contador << ". " << municipio << endl;
            contador++;
        }

        int seleccion;
        cout << "Ingrese el número del municipio que desea seleccionar: ";
        cin >> seleccion;
        cin.ignore();  /* Limpiar buffer de entrada*/

        return it->second[seleccion - 1];  /* Retorna el municipio seleccionado*/
    }
    return "";
}

/* Función para calcular las calorías diarias necesarias usando la fórmula de Harris - Benedict */
float calcularCaloriasDiarias(const Usuario& usuario) {
    float tdee;

    if (usuario.genero == "Masculino") {
        /* Fórmula para hombres*/
        tdee = 88.362 + (13.397 * usuario.peso) + (4.799 * usuario.altura) - (5.677 * usuario.edad);
    }
    else {
        /* Fórmula para mujeres*/
        tdee = 447.593 + (9.247 * usuario.peso) + (3.098 * usuario.altura) - (4.330 * usuario.edad);
    }

    /* Ajuste según el nivel de actividad física*/
    if (usuario.actividadFisica == "sedentario") {
        tdee *= 1.2;
    }
    else if (usuario.actividadFisica == "moderado") {
        tdee *= 1.55;
    }
    else if (usuario.actividadFisica == "alto") {
        tdee *= 1.9;
    }

    return tdee;
}

int main() {
	/*Mappa con los departamentos y municipios de Guatemala*/
    map<string, vector<string>> departamentos = {
        {"Alta Verapaz", {"Cobán", "Santa Cruz Verapaz", "San Cristóbal Verapaz", "Tactic", "Tamahú", "San Miguel Tucurú", "Panzós", "Senahú", "San Pedro Carchá", "San Juan Chamelco", "Lanquín", "Santa María Cahabón", "Chisec", "Chahal", "Fray Bartolomé de Las Casas", "Santa Catalina La Tinta", "Raxruhá"}},
        {"Baja Verapaz", {"Salamá", "San Miguel Chicaj", "Rabinal", "Cubulco", "Granados", "El Chol", "San Jerónimo", "Purulhá"}},
        {"Chimaltenango", {"Chimaltenango", "San José Poaquil", "San Martín Jilotepeque", "Comalapa", "Santa Apolonia", "Tecpán Guatemala", "Patzún", "San Miguel Pochuta", "Patzicía", "Santa Cruz Balanyá", "Acatenango", "Yepocapa", "San Andrés Itzapa", "Parramos", "Zaragoza", "El Tejar"}},
        {"Chiquimula", {"Chiquimula", "San José La Arada", "San Juan Ermita", "Jocotán", "Camotán", "Olopa", "Esquipulas", "Concepción Las Minas", "Quezaltepeque", "San Jacinto", "Ipala"}},
        {"El Progreso", {"Guastatoya", "Morazán", "San Agustín Acasaguastlán", "San Cristóbal Acasaguastlán", "El Jícaro", "Sansare", "Sanarate", "San Antonio La Paz"}},
        {"Escuintla", {"Escuintla", "Santa Lucía Cotzumalguapa", "La Democracia", "Siquinalá", "Masagua", "Tiquisate", "La Gomera", "Guanagazapa", "San José", "Iztapa", "Palín", "San Vicente Pacaya", "Nueva Concepción"}},
        {"Guatemala", {"Guatemala City", "Mixco", "Villa Nueva", "Santa Catarina Pinula", "San José Pinula", "San José del Golfo", "Palencia", "Chinautla", "San Pedro Ayampuc", "San Juan Sacatepéquez", "San Raymundo", "Chuarrancho", "Fraijanes", "Amatitlán", "Villa Canales", "San Miguel Petapa"}},
        {"Huehuetenango", {"Huehuetenango", "Chiantla", "Malacatancito", "Cuilco", "Nentón", "San Pedro Necta", "Jacaltenango", "Soloma", "Ixtahuacán", "Santa Bárbara", "La Libertad", "La Democracia", "San Miguel Acatán", "San Rafael La Independencia", "Todos Santos Cuchumatán", "San Juan Atitán", "Santa Eulalia", "San Mateo Ixtatán", "Colotenango", "San Sebastián Huehuetenango", "Tectitán", "Concepción Huista", "San Juan Ixcoy", "San Antonio Huista", "San Sebastián Coatán", "Santa Cruz Barillas", "Aguacatán", "San Rafael Petzal", "San Gaspar Ixchil", "Santiago Chimaltenango", "Santa Ana Huista"}},
        {"Izabal", {"Puerto Barrios", "Livingston", "El Estor", "Morales", "Los Amates"}},
        {"Jalapa", {"Jalapa", "San Pedro Pinula", "San Luis Jilotepeque", "San Manuel Chaparrón", "San Carlos Alzatate", "Monjas", "Mataquescuintla"}},
        {"Jutiapa", {"Jutiapa", "El Progreso", "Santa Catarina Mita", "Agua Blanca", "Asunción Mita", "Yupiltepeque", "Atescatempa", "Jerez", "El Adelanto", "Zapotitlán", "Comapa", "Jalpatagua", "Conguaco", "Moyuta", "Pasaco", "San José Acatempa", "Quesada"}},
        {"Petén", {"Flores", "San José", "San Benito", "San Andrés", "La Libertad", "San Francisco", "Santa Ana", "Dolores", "San Luis", "Sayaxché", "Melchor de Mencos", "Poptún"}},
        {"Quetzaltenango", {"Quetzaltenango", "Salcajá", "Olintepeque", "San Carlos Sija", "Sibilia", "Cabricán", "Cajolá", "San Miguel Siguilá", "San Juan Ostuncalco", "San Mateo", "Concepción Chiquirichapa", "San Martín Sacatepéquez", "Almolonga", "Cantel", "Huitán", "Zunil", "Colomba", "San Francisco La Unión", "El Palmar", "Coatepeque", "Génova", "Flores Costa Cuca", "La Esperanza", "Palestina de Los Altos"}},
        {"Quiché", {"Santa Cruz del Quiché", "Chiché", "Chinique", "Zacualpa", "Chajul", "Santo Tomás Chichicastenango", "Patzité", "San Antonio Ilotenango", "San Pedro Jocopilas", "Cunén", "San Juan Cotzal", "Joyabaj", "Nebaj", "San Andrés Sajcabajá", "San Miguel Uspantán", "Sacapulas", "San Bartolomé Jocotenango", "Canillá", "Chicamán", "Ixcán", "Pachalum"}},
        {"Retalhuleu", {"Retalhuleu", "San Sebastián", "Santa Cruz Muluá", "San Martín Zapotitlán", "San Felipe", "San Andrés Villa Seca", "Champerico", "Nuevo San Carlos", "El Asintal"}},
        {"Sacatepéquez", {"Antigua Guatemala", "Jocotenango", "Pastores", "Sumpango", "Santo Domingo Xenacoj", "Santiago Sacatepéquez", "San Bartolomé Milpas Altas", "San Lucas Sacatepéquez", "Santa Lucía Milpas Altas", "Magdalena Milpas Altas", "Santa María de Jesús", "Ciudad Vieja", "San Miguel Dueñas", "Alotenango", "San Antonio Aguas Calientes", "Santa Catarina Barahona"}},
        {"San Marcos", {"San Marcos", "San Pedro Sacatepéquez", "San Antonio Sacatepéquez", "Comitancillo", "San Miguel Ixtahuacán", "Concepción Tutuapa", "Tacaná", "Sibinal", "Tajumulco", "Tejutla", "San Rafael Pie de La Cuesta", "Nuevo Progreso", "El Tumbador", "San José El Rodeo", "Malacatán", "Catarina", "Ayutla", "Ocós", "San Pablo", "El Quetzal", "La Reforma", "Pajapita", "Ixchiguán"}},
        {"Santa Rosa", {"Cuilapa", "Pueblo Nuevo Viñas", "Oratorio", "San Rafael Las Flores", "Casillas", "San Juan Tecuaco", "Chiquimulilla", "Taxisco", "Santa María Ixhuatán", "Guazacapán", "Nueva Santa Rosa"}},
        {"Sololá", {"Sololá", "San José Chacayá", "Santa María Visitación", "Santa Lucía Utatlán", "Nahualá", "Santa Catarina Ixtahuacán", "Santa Clara La Laguna", "Concepción", "San Andrés Semetabaj", "Panajachel", "Santa Catarina Palopó", "San Antonio Palopó", "San Lucas Tolimán", "Santa Cruz La Laguna", "San Pablo La Laguna", "San Marcos La Laguna", "San Juan La Laguna", "San Pedro La Laguna", "Santiago Atitlán"}},
        {"Suchitepéquez", {"Mazatenango", "Cuyotenango", "San Francisco Zapotitlán", "San Bernardino", "San José El Ídolo", "Santo Domingo Suchitepéquez", "San Lorenzo", "Samayac", "San Pablo Jocopilas", "San Antonio Suchitepéquez", "San Miguel Panán", "San Gabriel", "Chicacao", "Patulul", "Santa Bárbara", "San Juan Bautista", "Santo Tomás La Unión", "Zunilito", "Pueblo Nuevo", "Río Bravo"}},
        {"Totonicapán", {"Totonicapán", "San Cristóbal Totonicapán", "San Francisco El Alto", "Santa Lucía La Reforma", "San Andrés Xecul", "Momostenango", "Santa María Chiquimula", "Santa María de Jesús"}},
        {"Zacapa", {"Zacapa", "Estanzuela", "Río Hondo", "Gualán", "Teculután", "Usumatlán", "Cabañas", "San Diego", "La Unión", "Huité"}},
        
    };

    /*Crear un objeto usuario para almacenar los datos personales*/
    Usuario usuario;

    /* Ingresar datos personales*/
    ingresarDatosPersonales(usuario);

    /* Mostrar los datos ingresados*/
    cout << "\nDatos Personales Ingresados:\n";
    cout << "Nombre: " << usuario.nombre << "\n";
    cout << "Altura: " << usuario.altura << " cm\n";
    cout << "Peso: " << usuario.peso << " kg\n";
    cout << "Edad: " << usuario.edad << "age\n";
    cout << "Género: " << usuario.genero << "\n";
    cout << "Dirección: " << usuario.direccion << "\n";
    cout << "Teléfono: " << usuario.telefono << "\n";
    cout << "Correo: " << usuario.correo << "\n";
    cout << "Nivel de actividad física: " << usuario.actividadFisica << "\n";
	
    /* Seleccionar un departamento*/
    string departamentoSeleccionado = seleccionarDepartamento(departamentos);

    /* Seleccionar un municipio  del departamento seleccionado*/
    string municip

ioSeleccionado = seleccionarMunicipio(departamentos, departamentoSeleccionado);

    /*Mostrar el departamento y municipio seleccionados*/
    cout << "\nDepartamento Seleccionado: " << departamentoSeleccionado << endl;
    cout << "Municipio Seleccionado: " << municipioSeleccionado << endl;

    /* Calcular las calorías diarias recomendadas*/
    float caloriasDiarias = calcularCaloriasDiarias(usuario);
    cout << "\nSu requerimiento calórico diario es: " << caloriasDiarias << " calorías.\n";

    return 0;
}
