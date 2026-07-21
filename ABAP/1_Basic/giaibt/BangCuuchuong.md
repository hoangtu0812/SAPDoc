_&---------------------------------------------------------------------_
_& Report Z_CNTT01_BANGCUUCHUONG
_&---------------------------------------------------------------------\*
_&
_&---------------------------------------------------------------------\*
REPORT Z_CNTT01_BANGCUUCHUONG.

DATA:
gv_gta TYPE i VALUE 1,
gv_gtb TYPE i,
gv_kq TYPE i.
DO 8 TIMES.
gv_gtb = 0.
gv_gta += 1 .
DO 10 TIMES.
gv_gtb += 1.
gv_kq = gv_gta _ gv_gtb.
WRITE: /, gv_gta , ' _ ' , gv_gtb , ' = ', gv_kq.
ENDDO.
WRITE: /,'----------------------------------------------------'.
ENDDO.
