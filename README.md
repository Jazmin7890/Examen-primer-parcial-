#include <iostream>
#include <string>
using namespace std;

enum class Estado { APAGADO, ACTIVO, ERROR };

struct Ubicacion {
    string zona;
    int posicion;
};

struct Sensor {
    int id;
    Estado estado;
    Ubicacion ubicacion;
    double lectura;
    unsigned banderas;
};

struct Indicadores {
    unsigned energia : 1, conexion : 1;
    unsigned alarma : 1, modo : 2;
};

const unsigned BIT_ALARMA = 2;
const unsigned MASCARA_ALARMA = 1u << BIT_ALARMA;

class Sistema {
public:

    Sensor sensores[3] = {
        {101, Estado::ACTIVO, {"Laboratorio", 1}, 24.5, 0u},
        {102, Estado::ERROR, {"Laboratorio", 2}, 91.0, MASCARA_ALARMA},
        {103, Estado::APAGADO, {"Pasillo", 1}, 0.0, 0u}
    };

    // TODO 1
    void mostrarResumen() const {
        for (int i = 0; i < 3; ++i) {
            const Sensor& s = sensores[i];

            cout << "ID " << s.id
                 << ", Zona: " << s.ubicacion.zona
                 << ", Posicion: " << s.ubicacion.posicion
                 << ", Lectura: " << s.lectura << '\n';
        }
    }

    // TODO 2
    int contarActivos() const {
        int total = 0;

        for (int i = 0; i < 3; ++i) {
            if (sensores[i].estado == Estado::ACTIVO) {
                total++;
            }
        }

        return total;
    }

    // TODO 3
    bool tieneBandera(int i, unsigned mascara) const {
        return (sensores[i].banderas & mascara) != 0;
    }

    // TODO 4
    void activarBandera(int i, unsigned bit) {
        unsigned mascara = 1u << bit;
        sensores[i].banderas |= mascara;
    }

    // TODO 5
    Indicadores construirIndicadores() const {
        Indicadores panel{};

        panel.energia = 1;
        panel.conexion = 1;
        panel.alarma = 1;
        panel.modo = 2;

        return panel;
    }
};

int main() {

    Sistema sistema;

    sistema.mostrarResumen();

    cout << "Sensores activos: "
         << sistema.contarActivos() << '\n';

    cout << "Sensor 102 con alarma: "
         << (sistema.tieneBandera(1, MASCARA_ALARMA) ? "SI" : "NO")
         << '\n';

    sistema.activarBandera(0, BIT_ALARMA);

    cout << "Sensor 101 con alarma despues de activar: "
         << (sistema.tieneBandera(0, MASCARA_ALARMA) ? "SI" : "NO")
         << '\n';

    Indicadores panel = sistema.construirIndicadores();

    cout << "Panel: energia=" << panel.energia
         << ", conexion=" << panel.conexion
         << ", alarma=" << panel.alarma
         << ", modo=" << panel.modo
         << '\n';

    return 0;
}
