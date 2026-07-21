*&---------------------------------------------------------------------*
*& Report Z_CNTT01_CACULATOR
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT Z_CNTT01_CACULATOR.

PARAMETERS:
    gv_gt1 TYPE i,
    gv_gt2 TYPE i,
    gv_pt TYPE c LENGTH 1.
DATA:
    gv_kq TYPE i.


CASE gv_pt.
  WHEN '*'.
    gv_kq = gv_gt1 * gv_gt2.
    WRITE: / 'Tich = ' , gv_kq.
  WHEN '/'.
    gv_kq =  gv_gt1 / gv_gt2.
    WRITE: / 'Thuong = ' , gv_kq .
  WHEN '+'.
    gv_kq = gv_gt1 + gv_gt2.
    WRITE: / 'Tong = ' , gv_kq.
  WHEN '-'.
    gv_kq = gv_gt1 - gv_gt2.
    WRITE: / 'Hieu = ' , gv_kq .
ENDCASE.