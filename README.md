# Consultas-triggers
TRIGGER 
EN UN CAMPO EN LA TABLA TUTOR LLAMADO “NUMEROTUTORIAS” HACER QUE EL CAMPO SE INCREMENTA CADA VEZ QUE EL TUTOR HA CONCLUIDO DE DAR UNA TUTORIA.
GO
CREATE OR ALTER TRIGGER Aumentar_Tutorias
   ON TUTORIA
   FOR INSERT
   AS
   SET NOCOUNT ON
   UPDATE TUTOR  SET TUTOR.NUMERO_TUTORIA = TUTOR.NUMERO_TUTORIA + INSERTED.CANTIDAD FROM INSERTED
     INNER JOIN TUTOR ON  TUTOR.ID_TUTOR= INSERTED.ID_TUTOR
   GO

   INSERT INTO TUTORIA VALUES(18,1,1,5,2,1)

   SELECT * FROM TUTOR
   SELECT * FROM TUTORIA
   
2--------------------------------------
PROCEDIMIENTO ALMACENADO
QUE RECIBA COMO PARÁMETRO EL NOMBRE DE UN PROFESOR Y MUESTRE TODOS LOS ESTUDIANTES QUE HA TENIDO.
GO
CREATE PROCEDURE T_TUTOR
@NOMBRE VARCHAR(50) AS
SELECT T.NOMBRE_TUTOR,T.APELLIDO_TUTOR,R.NOMBRE_TUTORADO, COUNT(D.ID_TUTORADO) AS TOTAL_TUTORIAS  FROM TUTORIA D
 INNER JOIN TUTOR T ON T.ID_TUTOR=D.ID_TUTOR
 INNER JOIN TUTORADO R ON R.ID_TUTORADO=D.ID_TUTORADO
 WHERE T.NOMBRE_TUTOR=@NOMBRE GROUP BY T.NOMBRE_TUTOR,T.APELLIDO_TUTOR,R.NOMBRE_TUTORADO
GO
EXEC T_TUTOR 'Roberth'


3---------------------------------------------
CURSOR
QUE MUESTRE POR AÑO Y MES CUANTO LA EMPRESA HA PAGADO EN TUTORÍAS A PROFESORES, CUANTO HA RECIBIDO DE LOS ESTUDIANTES, Y CUAL ES LA UTILIDAD.
DECLARE @AÑO CHAR(5),@MES CHAR(12),@PRECIO_TUTORIA CHAR(20),@VALOR CHAR(19),@TOTAL_UTILIDADES CHAR(19)
	DECLARE CURSOR1 CURSOR 
	FOR SELECT F.AÑO,F.MES,
		   SUM(P.PRECIO_TUTORIA) AS TOTAL_RECIBIDO_ESTUDIANTES,
		   SUM(C.VALOR) AS TOTAL_INVERTIDO,
		   SUM(C.VALOR-P.PRECIO_TUTORIA) AS TOTAL_UTILIDADES
		   FROM FACTURA AS F
	       JOIN PAGO_TUTORIA AS P ON P.ID_PAGO=F.ID_PAGO
           JOIN PLAN_COMPRA AS C ON C.ID_PLANCOMPRA=F.ID_PLANCOMPRA
	       GROUP BY F.AÑO,F.MES,P.PRECIO_TUTORIA,C.VALOR
	OPEN CURSOR1
	FETCH CURSOR1 INTO  @AÑO,@MES,@PRECIO_TUTORIA,@VALOR,@TOTAL_UTILIDADES
	PRINT 'AÑO       MES     TOTAL_RECIBIDO_ESTUDIANTES    TOTAL_INVERTIDO       TOTAL_UTILIDADES'
	PRINT'----------------------------------------------------------------------------------------------'
	WHILE @@FETCH_STATUS=0
	BEGIN
	PRINT @AÑO +SPACE(5)+@MES +SPACE(5)+@PRECIO_TUTORIA +SPACE(5)
	+@VALOR +SPACE(5)+@TOTAL_UTILIDADES +SPACE(5)
	FETCH CURSOR1 INTO  @AÑO,@MES,@PRECIO_TUTORIA,@VALOR,@TOTAL_UTILIDADES
	END
	CLOSE CURSOR1
	DEALLOCATE CURSOR1
	
	
4-------------------------------------
IREPORT
UN REPORTE EN BARRAS DE LA CANTIDAD DE CURSOS POR TUTOR
SELECT CASE 
WHEN R.ID_TUTOR=1 THEN 'Klever'
WHEN R.ID_TUTOR=2 THEN 'Jorge'
WHEN R.ID_TUTOR=3 THEN 'Roberth'
 END AS TUTOR,
COUNT(ID_TUTORIA) as TOTAL_CURSOS_DE_TUTORIAS
FROM TUTORIA AS R INNER JOIN TUTOR AS T ON T.ID_TUTOR=R.ID_TUTOR
GROUP BY R.ID_TUTOR
