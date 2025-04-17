import pygame as p
import os #windows ios kalilinux işletim sistemini belirler


class GameState:
    def __init__(self):
        self.board = [
            ["bR", "bN", "bB", "bQ", "bK", "bB", "bN", "bR"],
            ["bp", "bp", "bp", "bp", "bp", "bp", "bp", "bp"],
            ["--", "--", "--", "--", "--", "--", "--", "--"],
            ["--", "--", "--", "--", "--", "--", "--", "--"],
            ["--", "--", "--", "--", "--", "--", "--", "--"],
            ["--", "--", "--", "--", "--", "--", "--", "--"],
            ["wp", "wp", "wp", "wp", "wp", "wp", "wp", "wp"],
            ["wR", "wN", "wB", "wQ", "wK", "wB", "wN", "wR"]
        ]
        self.white_to_move = True  # Beyaz başlar


WIDTH = HEIGHT = 512 #geniş ve buyuklugu ayarlar
DIMENSION = 8 #uzunlugu ayarlar
SQ_SIZE = HEIGHT // DIMENSION # uzunlugu ve buyuklugu ayarlar
MAX_FPS = 15 #oyun hızını ayarlar
IMAGES = {} #pngleri kodlara ekler


def load_images():
    pieces = ["wp", "wR", "wN", "wB", "wK", "wQ", "bp", "bR", "bN", "bB", "bK", "bQ"] #fotoraflar ve pngleri taşların fotograflarını kodun yuzeyine koyar
    base_path = os.path.join(os.path.dirname(__file__), "images") #kodun işletim sistemine fotgrafları ekler
    for piece in pieces: # piece nin içindeki satırları ve strigleri pieces in içine yazar
        image_path = os.path.join(base_path, piece + ".png") #pngleri kodun işletim sistemine ekler
        if os.path.exists(image_path): # işletim sisteminden cıkar
            IMAGES[piece] = p.transform.scale(p.image.load(image_path), (SQ_SIZE, SQ_SIZE)) # ölçekleri degiştirmek için  ve eklemek için kollanılır


def mouse_click_to_square(location):#bu kod mouse tıklayışlarını okur
    x, y = location #taşların hitboxsunu belirler
    col = x // SQ_SIZE#taşların hitboxsunu belirler
    row = y // SQ_SIZE#taşların hitboxsunu belirler
    if col < 0 or col >= DIMENSION or row < 0 or row >= DIMENSION:#bir satranç tahtasının sınırları içinde olup olmadığınızı kontrol ediyor
        return None
    return row, col 


def is_valid_pawn_move(board, start, end, white_to_move):#piyon hareketleri
    start_row, start_col = start 
    end_row, end_col = end 
    move_direction = -1 if white_to_move else 1 
    start_piece = board[start_row][start_col] 
    end_piece = board[end_row][end_col]

    if start_col == end_col and end_piece == "--": #giden taşın ilerlemesini saglar ilerledikten sonra daha ilerletemeyiz
        if end_row == start_row + move_direction: 
            return True # yukardakı kodların sonsuza kadar devam etmesini saglar ancak şah yenilene kadar 
        elif (end_row == start_row + 2 * move_direction and r
              (start_row == 6 if white_to_move else start_row == 1) and 
              board[start_row + move_direction][start_col] == "--"):
            return True

    if abs(end_col - start_col) == 1 and end_row == start_row + move_direction:
        if end_piece != "--" and start_piece[0] != end_piece[0]:
            return True
    return False


def is_valid_rook_move(board, start, end): #kalenin harketleri
    start_row, start_col = start
    end_row, end_col = end
    if start_row != end_row and start_col != end_col:
        return False

    if start_row == end_row:
        step = 1 if end_col > start_col else -1
        for c in range(start_col + step, end_col, step):
            if board[start_row][c] != "--":
                return False
    else:
        step = 1 if end_row > start_row else -1
        for r in range(start_row + step, end_row, step):
            if board[r][start_col] != "--":
                return False
    return True


def is_valid_knight_move(start, end):#at hareketleri
    start_row, start_col = start
    end_row, end_col = end
    row_diff = abs(start_row - end_row)
    col_diff = abs(start_col - end_col)
    return (row_diff == 2 and col_diff == 1) or (row_diff == 1 and col_diff == 2)


def is_valid_bishop_move(board, start, end):# filin hareketleri
    start_row, start_col = start
    end_row, end_col = end

    if abs(start_row - end_row) != abs(start_col - end_col):
        return False

    step_row = 1 if end_row > start_row else -1
    step_col = 1 if end_col > start_col else -1

    for i in range(1, abs(end_row - start_row)):
        if board[start_row + i * step_row][start_col + i * step_col] != "--":
            return False
    return True


def is_valid_queen_move(board, start, end):#vezir hareketleri
    return is_valid_rook_move(board, start, end) or is_valid_bishop_move(board, start, end)


def is_valid_king_move(start, end):#şah hareketleri
    start_row, start_col = start
    end_row, end_col = end
    return abs(start_row - end_row) <= 1 and abs(start_col - end_col) <= 1


def is_valid_move(board, start, end, white_to_move):#taşların hareketlerini dogrular
    start_row, start_col = start
    end_row, end_col = end

    start_piece = board[start_row][start_col]
    end_piece = board[end_row][end_col]

    if (white_to_move and start_piece[0] != "w") or (not white_to_move and start_piece[0] != "b"):
        return False#beyaz başlamasssa oyun devam etmez

    if end_piece != "--" and start_piece[0] == end_piece[0]:
        return False #taş hareket etiginden sonra oraya ilerler

    piece_type = start_piece[1]

    if piece_type == "p":# taşların sayıları ve yendiginde neler oldugunu
        return is_valid_pawn_move(board, start, end, white_to_move)
    elif piece_type == "R":
        return is_valid_rook_move(board, start, end)
    elif piece_type == "N":
        return is_valid_knight_move(start, end)
    elif piece_type == "B":
        return is_valid_bishop_move(board, start, end)
    elif piece_type == "Q":
        return is_valid_queen_move(board, start, end)
    elif piece_type == "K":
        return is_valid_king_move(start, end)

    return False


def move_piece(board, start, end, game_state): #bir oyuncunun birrden fazla oynamamasını saglar
    if is_valid_move(board, start, end, game_state.white_to_move):
        start_row, start_col = start
        end_row, end_col = end
        piece = board[start_row][start_col]
        board[start_row][start_col] = "--"
        board[end_row][end_col] = piece
        game_state.white_to_move = not game_state.white_to_move


def drawGameState(screen, gs, selected_square): # tahtanın karelerini belirler
    drawBoard(screen)
    drawPieces(screen, gs.board)
    draw_selected_square(screen, selected_square)


def drawBoard(screen):# tahatadaki kareleri boyar
    colors = [p.Color("white"), p.Color("gray")]
    for r in range(DIMENSION):
        for c in range(DIMENSION):
            color = colors[(r + c) % 2]
            p.draw.rect(screen, color, p.Rect(c * SQ_SIZE, r * SQ_SIZE, SQ_SIZE, SQ_SIZE))


def drawPieces(screen, board):# tahtanın uzerindeik karelerin buyuklugunu ayarlar
    for r in range(DIMENSION):
        for c in range(DIMENSION):
            piece = board[r][c]
            if piece != "--":
                screen.blit(IMAGES[piece], p.Rect(c * SQ_SIZE, r * SQ_SIZE, SQ_SIZE, SQ_SIZE))


def draw_selected_square(screen, selected_square):#taşlara tıkladıgımızada dogrusuna mı tıkladıgımızı gösterir
    if selected_square != ():
        row, col = selected_square
        s = p.Surface((SQ_SIZE, SQ_SIZE))
        s.set_alpha(100)
        s.fill(p.Color("blue"))
        screen.blit(s, (col * SQ_SIZE, row * SQ_SIZE))


def is_checkmate(board, white_to_move): #şah yendiginde oyun biter
    king_piece = "wK" if white_to_move else "bK"
    king_positions = [(r, c) for r in range(DIMENSION) for c in range(DIMENSION) if board[r][c] == king_piece]
    if not king_positions:
        return True
    return False


def main(): #oyunun ana manusunu ve yuzeyini yapar
    p.init()
    screen = p.display.set_mode((WIDTH, HEIGHT))
    clock = p.time.Clock()
    screen.fill(p.Color("white"))#yerin beyaz oldugunu ayarlar
    gs = GameState()
    load_images()

    running = True #hareket etmesini saglar
    selected_square = ()
    player_clicks = [] # tıklmasını saglar

    while running:
        for e in p.event.get():
            if e.type == p.QUIT:
                running = False
            elif e.type == p.MOUSEBUTTONDOWN:
                location = p.mouse.get_pos()
                square = mouse_click_to_square(location)
                if square:
                    row, col = square
                    if selected_square == (row, col):
                        selected_square = ()
                        player_clicks = []
                    else:
                        selected_square = (row, col)
                        player_clicks.append(selected_square)

                    if len(player_clicks) == 2:
                        start_square = player_clicks[0]
                        end_square = player_clicks[1]
                        move_piece(gs.board, start_square, end_square, gs)
                        selected_square = ()
                        player_clicks = []

        # Şah ve Mat kontrolü
        if is_checkmate(gs.board, gs.white_to_move):#şah ve mat oldugunu belirler
            print("Şah ve Mat! Oyun bitti!")
            running = False

        drawGameState(screen, gs, selected_square)
        clock.tick(MAX_FPS)#oyunun hızını artırır
        p.display.flip()


if __name__ == "__main__":
    main()
