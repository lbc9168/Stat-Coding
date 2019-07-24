# Some useful python codes for dealing with data

Creating repeating R codes

```python
for i in [37, 45, 12, 47, 13, 1, 28, 5, 22]:
    print(''' dbGetQuery(FIADB, \"SELECT ps.EVAL_GRP, ps.cn PlotID,
                         Sum([EXPCURR]*[CONDPROP_UNADJ]*[ADJ_EXPCURR]) 
                         AS [tt_acres_ftg_plt_age_plot], 

                         peg.STATECD, ps.COUNTYCD, c.FORTYPCD,
                         c.STDAGE, c.STDORGCD, rft.TYPGRPCD, ftg.MEANING
                                        
                         FROM pop_eval_grp peg,
                         plotsnap ps,
                         cond c,
                         ref_forest_type rft,
                         ref_forest_type_group ftg
                                        
                         WHERE ps.cn = c.plt_cn
                         AND peg.cn = ps.eval_grp_cn
                         AND c.fortypcd = rft.value
                         AND rft.typgrpcd = ftg.value
                                        
                         AND c.cond_status_cd = 1
                         AND (c.siteclcd = 1 Or c.siteclcd = 2 or c.siteclcd = 3 or
                         c.siteclcd = 4 or c.siteclcd = 5 or c.siteclcd = 6)
                         AND c.reservcd = 0
                         AND rft.TYPGRPCD = 160
                         AND ('''
                         , "ps.eval_grp = ", i, '\b2016', "or ps.eval_grp = ", i, '\b2015', "or ps.eval_grp =", i, '\b2014', 'or ps.eval_grp = ', i, '\b2013', '\n'
                         , "or ps.eval_grp = ", i, '\b2012', "or ps.eval_grp = ", i, '\b2011', "or ps.eval_grp =", i, '\b2010', 'or ps.eval_grp = ', i, '\b2019', '\n'
                         , "or ps.eval_grp = ", i, '\b2008', "or ps.eval_grp = ", i, '\b2007', "or ps.eval_grp =", i, '\b2006', 'or ps.eval_grp = ', i, '\b2005', '\n'
                         , "or ps.eval_grp = ", i, '\b2004', "or ps.eval_grp = ", i, '\b2003', "or ps.eval_grp =", i, '\b2002', 'or ps.eval_grp = ', i, '\b2001', '\n'
                         , "or ps.eval_grp = ", i, '\b2000', "or ps.eval_grp = ", i, '\b1999', "or ps.eval_grp =", i, '\b1998', 'or ps.eval_grp = ', i, '\b1997', '\n'
                         , "or ps.eval_grp = ", i, '\b1996', "or ps.eval_grp = ", i, '\b1995', "or ps.eval_grp =", i, '\b1994', 'or ps.eval_grp = ', i, '\b1993', '\n'
                         , "or ps.eval_grp = ", i, '\b1992', "or ps.eval_grp = ", i, '\b1991', "or ps.eval_grp =", i, '\b1990', ')\n'

                         , '''GROUP BY ps.eval_grp, peg.STATECD,ps.COUNTYCD, 
                                        c.STDAGE, c.STDORGCD,rft.TYPGRPCD, 
                                        ftg.MEANING, ps.cn\") \n''')

```

`i` represents state codes

```python
for i in [37, 45, 12, 47, 13, 1, 28, 5, 22]:
    print(''' dbGetQuery(FIADB, \"SELECT ps.EVAL_GRP, ps.cn PlotID,
                         Sum([EXPCURR]*[CONDPROP_UNADJ]*[ADJ_EXPCURR]) 
                         AS [tt_acres_ftg_plt_age_plot], 

                         peg.STATECD, ps.COUNTYCD, c.FORTYPCD,
                         c.STDAGE, c.STDORGCD, rft.TYPGRPCD, ftg.MEANING
                                        
                         FROM pop_eval_grp peg,
                         plotsnap ps,
                         cond c,
                         ref_forest_type rft,
                         ref_forest_type_group ftg
                                        
                         WHERE ps.cn = c.plt_cn
                         AND peg.cn = ps.eval_grp_cn
                         AND c.fortypcd = rft.value
                         AND rft.typgrpcd = ftg.value
                                        
                         AND c.cond_status_cd = 1
                         AND (c.siteclcd = 1 Or c.siteclcd = 2 or c.siteclcd = 3 or
                         c.siteclcd = 4 or c.siteclcd = 5 or c.siteclcd = 6)
                         AND c.reservcd = 0
                         AND rft.TYPGRPCD = 160
                         AND ('''
                         ps.eval_grp = %s2016, or ps.eval_grp = %s2015, or ps.eval_grp = %s2014, or ps.eval_grp = %s2013, 
                         or ps.eval_grp = %s2012, or ps.eval_grp = %s2011, or ps.eval_grp = %s2010, or ps.eval_grp = %s2019, 
                         or ps.eval_grp = %s2008, or ps.eval_grp = %s2007, or ps.eval_grp = %s2006, or ps.eval_grp = %s2005, 
                         or ps.eval_grp = %s2004, or ps.eval_grp = %s2003, or ps.eval_grp = %s2002, or ps.eval_grp = %s2001, 
                         or ps.eval_grp = %s2000, or ps.eval_grp = %s1999)

                         , GROUP BY ps.eval_grp, peg.STATECD,ps.COUNTYCD, 
                                        c.STDAGE, c.STDORGCD,rft.TYPGRPCD, 
                                        ftg.MEANING, ps.cn''' % (i,i,i,i,i,i,i,i,i,i,i,i,i,i,i,i,i,i))

                        # there are 18 'i's since there are 18 '%s' 
```


