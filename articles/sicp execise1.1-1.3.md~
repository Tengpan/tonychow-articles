##sicp第一章课后练习
###1.1
    10 = 10
    (+ 5 3 4) = 12
    (- 9 1) = 8
    (/ 6 2) = 3
    (+ (* 2 4) (- 4 6)) = (+ 8 -2) = 6
    (define a 3)
    (define b (+ a 1))
    (+ a b (* a b)) = (+ 3 (+ 3 1) (* 3 (+ 3 1)))
                    = (+ 3 4 (* 3 4))
                    = (+ 3 4 12)
                    = 19
    (= a b) = False/#f                        
    (if (and (> b a) (< b (* a b)))
        b
        a) = 4
    (cond ((= a 4) 6)
          ((= b 4) (+ 6 7 a))
          (else 25)) = 16
    (+ 2 (if (> b a) b a)) = 6
    (* (cond ((> a b) a)
             ((< a b) b)
             (else -1))
       (+ a 1)) = (* 4 5) = 16

###1.2
    (/ (+ 5
          4
          (- 2
             (- 3 
               (+ 6 (/ 4 5)))))
       (* 3 (- 6 2) (- 2 7))) = -37/150
       
###1.3
    (define (get_lager_squares_sum a b c)
      (cond ((and (or (> b a) (= b a))
                  (or (> c a) (= c a))) 
              (+ (* b b) (* c c)))
            ((and (or (> a b) (= a b))
                  (or (> c b) (= c b))) 
              (+ (* a a) (* c c)))
            ((and (or (> a c) (= a c))
                  (or (> a b) (= a b)))
              (+ (* a a) (* b b)))))
    