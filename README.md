# PrimerRepositirio
Es la primera version de nuestro repositorio de nuetro proyecto 
package control_calificaciones_6a_verano.cc6averano;

import android.app.Activity;
import android.database.Cursor;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.Window;
import android.widget.ArrayAdapter;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.Toast;


public class ActualizarCalificaciones extends Activity {
    Spinner sp_ac_cal;
    EditText cal1,cal2,cal3,cal4;
    boolean califAgregadas;

    CrearTablas ct;
    Cursor c;
    int renglon=0;
    int idAlumno=0;

    String acumulaAlumno[][]= new String[15][2];
    String alumnos[];

    String idCalif,claveAlumno,c1,c2,c3,c4;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_actualizar_calificaciones);

        LlenarSpinnerAlumnos();
    }

    public void LlenarSpinnerAlumnos()
    {
        sp_ac_cal= (Spinner) findViewById(R.id.spinnerActualizar);

        ct = new CrearTablas(this);
        Cursor c=ct.ObtenerAlumnos();

        int i=0;
        int contador=0;

        while(c.moveToNext())
        {
            int columnaId= c.getColumnIndex(TablaAlumno.ID_ALumno);
            String id=c.getString(columnaId);

            acumulaAlumno[renglon][0]=String.valueOf(id);

            int columnaAlumno=c.getColumnIndex(TablaAlumno.ALUMNO);
            String alumno= c.getString(columnaAlumno);

            acumulaAlumno[renglon][1]=String.valueOf(alumno);

            i=i+1;
            renglon=renglon+1;
        }
        alumnos = new String[renglon];
        for(int j=0;j<renglon;j++)
        {
            alumnos[j]=String.valueOf(acumulaAlumno[j][1]);
        }
        ArrayAdapter adaptador =
                new ArrayAdapter(this, android.R.layout.simple_spinner_item, alumnos);
        sp_ac_cal.setAdapter(adaptador);
    }

    public void eventoMostrarCalif(View v)
    {
        ct = new CrearTablas(this);
        cal1 = (EditText) findViewById(R.id.etC1);
        cal2 = (EditText) findViewById(R.id.etC2);
        cal3 = (EditText) findViewById(R.id.etC3);
        cal4 = (EditText) findViewById(R.id.etC4);

        //obener el nombre del alumno del spinner
        String nombreAlumno= sp_ac_cal.getSelectedItem().toString();

        for(int j=0;j<renglon;j++)
        {
            if(acumulaAlumno[j][1].equals(nombreAlumno))
            {
                idAlumno=Integer.parseInt(String.valueOf(acumulaAlumno[j][0]));
            }
        }
        Cursor c= ct.obtenercalificacionesEstudiant(idAlumno);
        while(c.moveToNext())
        {
            int colid=c.getColumnIndex(TablaCalificaciones.ID_CALIF);
            idCalif=c.getString(colid);

            int ca=c.getColumnIndex(TablaCalificaciones.CLAVE_ALUMNO);
            claveAlumno=c.getString(ca);

            int cal1= c.getColumnIndex(TablaCalificaciones.CALIFICACION1);
            c1=c.getString(cal1);

            int cal2=c.getColumnIndex(TablaCalificaciones.CALIFICACION2);
            c2= c.getString(cal2);

            int cal3=c.getColumnIndex(TablaCalificaciones.CALIFICACION3);
            c3= c.getString(cal3);

            int cal4= c.getColumnIndex(TablaCalificaciones.CALIFICACION4);
            c4= c.getString(cal4);

        }
        cal1.setText(String.valueOf(c1));
        cal2.setText(String.valueOf(c2));
        cal3.setText(String.valueOf(c3));
        cal4.setText(String.valueOf(c4));

        String mensaje="";
        int duracion=1;

        if(califAgregadas)
        {
            mensaje="Calificaciones mostradas correctamente";
            duracion=Toast.LENGTH_SHORT;
        }
        else
        {
            mensaje="No tiene calificacione para mostrar";
            duracion=Toast.LENGTH_LONG;
        }
        Toast.makeText(this, mensaje,duracion).show();
    }

    public  void eventoActualizarCalif(View v)
    {
        ct= new CrearTablas(this);
        cal1= (EditText) findViewById(R.id.etC1);
        cal2= (EditText) findViewById(R.id.etC2);
        cal3= (EditText) findViewById(R.id.etC3);
        cal4= (EditText) findViewById(R.id.etC4);

        //obtener elemento seleccionado en el spiner
        String nombreAlumno=sp_ac_cal.getSelectedItem().toString();

        for(int j=0;j<renglon;j++)
        {
            if(acumulaAlumno[j][1].equals(nombreAlumno))
            {
                idAlumno=Integer.parseInt(String.valueOf(acumulaAlumno[j][0]));
            }

            String texto="";
            int identificadorAlumno=idAlumno;
            int c1=Integer.parseInt(String.valueOf(cal1.getText().toString()));
            int c2=Integer.parseInt(String.valueOf(cal2.getText().toString()));
            int c3=Integer.parseInt(String.valueOf(cal3.getText().toString()));
            int c4=Integer.parseInt(String.valueOf(cal4.getText().toString()));

            long califActualizadas=ct.actualizarCalificaciones(identificadorAlumno,c1,c2,c3,c4);

            String mensaje="";
            int duracion=1;
            if(califActualizadas !=0)
            {
                mensaje="Calificaciones actualizadas correctamente";
                duracion= Toast.LENGTH_SHORT;
            }
            else
            {
                mensaje="No se actualizaron las calificacinoes";
                duracion=Toast.LENGTH_LONG;
            }
            Toast.makeText(this, mensaje, duracion).show();
        }

    }

}
